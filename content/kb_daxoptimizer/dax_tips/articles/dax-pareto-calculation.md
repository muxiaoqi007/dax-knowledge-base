---
title: "DAX Pareto Calculation"
url: "https://dax.tips/2023/04/17/dax-pareto-calculation"
published: "2023-04-16"
updated: "2023-12-05"
source: "dax.tips"
---

I always enjoy it when we get new DAX functions, especially so for the new set of [WINDOW](https://learn.microsoft.com/en-us/dax/window-function-dax) Functions recently added. As part of the April 2023 release of Power BI Desktop, we now have a [RANK](https://learn.microsoft.com/en-us/dax/rank-function-dax) function and the ability to use a measure to control the order within the existing WINDOW function.

The first thing that sprung to my mind was to see how a Pareto calculation might leverage the new capability.

The basic idea of a Pareto calculation is to create a curve like representation of data ordered from largest to smallest.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2023/04/image-1.png?resize=863%2C643&ssl=1)

A great example of this is to consider a country’s population organised and sorted by city. The first item on the list might be the country’s largest city, followed by the next largest. If a running total is applied, you can quickly determine many interesting points, like, how few cities account for 50% of the overall percentile of the total population.

The same approach can be helpful in a business setting, e.g. what products account for *n*% of overall sales for a mix of dimensions.

## The DAX

I’ll cut to the chase and share a snippet of DAX you can run against a [model](https://1drv.ms/u/s!AtDlC2rep7a-goR49jYY3bc1-c0IiQ?e=kErUcB) based on AdventureWorks data.

```dax
DEFINE

    MEASURE FactInternetSales[SalesAmount] =

        CALCULATE ( SUM ( FactInternetSales[SalesAmount] ) )



EVALUATE

	SUMMARIZECOLUMNS (

		// GROUP BY COLUMNS

	    'DimProduct'[Color],

	    

	    // MEASURES

	    "Sales", [SalesAmount],

	    

	    "Old Rank Function", RANKX ( ALLSELECTED ( 'DimProduct'[Color] ), [SalesAmount] ),

	    

	    "New Rank Function",

	        RANK (

	            DENSE,

	            ALLSELECTED ( 'DimProduct'[Color] ),

	            ORDERBY ( [SalesAmount], DESC )

	        ),

	        

	    "Pareto" , 

	    	SUMX(

	    		WINDOW(

	    			0 , ABS ,

	    			0 , REL ,

	    			ALLSELECTED ( 'DimProduct'[Color] ),

	            	ORDERBY ( [SalesAmount], DESC )

	    		),[SalesAmount]) ,

	    		

	            

	    "Pareto %" , 

	    

	    	VAR TotalSales = CALCULATE([SalesAmount], ALLSELECTED ( 'DimProduct'[Color] ))

	    	VAR ParetoSales = 

		    	SUMX(

		    		WINDOW(

		    			0 , ABS ,

		    			0 , REL ,

		    			ALLSELECTED ( 'DimProduct'[Color] ),

		            	ORDERBY ( [SalesAmount], DESC )

		    		),[SalesAmount])

		    RETURN 

		    	ParetoSales / TotalSales

	)

	ORDER BY 

		[Sales] DESC

```

![Image showing DAX Pareto calculation along with the results of the query when run against a model using AventureWorks data](https://i0.wp.com/dax.tips/wp-content/uploads/2023/04/image.png?resize=817%2C1024&ssl=1)

Lines 2 and 3 create a simple measure used multiple times throughout the rest of the query.

Line 8 defines how we intend to group the query. In this case, the query groups by product color, which is essential to note for later in the new RANK and WINDOW Functions.

Line 11 creates a simple baseline measure to show the value of our [SalesAmount] measure per color. This column sorts the overall query at lines 46 and 47 to help us visually inspect and understand the remaining measures.

Line 13 shows how to create a measure using the existing RANKX Function. The results for this version of the calculation appear in the third from the left column in the results. This calculation gets included to provide a comparison.

## RANK ordered by measure

The fun starts at line 15. Here the new RANK function gets used with an ORDERBY helper function that uses our measure from Line 3. As expected, the values produced by the new RANK function match those in column 3.

The query plan generated for the new RANK function is marginally better than for RANKX over this tiny dataset.

## Pareto calculation

The DAX to generate the cumulative (running) total for sales is at line 22. The code shown in lines 23 through 29 will resemble what you might add to a calculation in your model.

The SUMX function provides a looping mechanism to add values for colors relevant to each row.

The first four parameters of the WINDOW function control the cumulative nature of the calculation. The first two parameters (0 and ABS) state the list of Colors should always begin at the absolute position of 0 or the start of the ordered list of colors.

The 3rd and fourth parameters (0 and REL) tell the WINDOW function the end of the range should always match the current position belonging to the color.

The magic that makes the Pareto calculation possible is line 41, where an ORDERBY function uses the [SalesAmount] measure to control how the list of colors gets ordered for the WINDOW function to determine which colors get included in the valid range.

Applying all this means, in the case of the first row, the SUMX function only iterates over a single value, “Red”. For the second row, the SUMX iterates over “Red” and “Black”. The third-row SUMX iterates over “Red”, “Black” and “Silver” etc.

## Pareto %

The natural extension to the Pareto calculation is to convert the running total to a percentage of the overall total. To achieve this, the calculation adds a step to determine the overall total in line 34. This total value gets used in line 44 to create the ratio. When added to a Power BI visual, you can see the shape of the curve, and hovering a point shown in this case, 82.06% of sales consist of just three colors.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2023/04/image-1.png?resize=863%2C643&ssl=1)

## Summary

The new DAX WINDOW functions provide an alternative method to help solve a common business request. Be mindful that the syntax for calculation needs to match the columns used in the visual; otherwise, you may get confusing results. You will need to consider this when retrofitting the approach to your model.

4.7
18
votes

Article Rating
