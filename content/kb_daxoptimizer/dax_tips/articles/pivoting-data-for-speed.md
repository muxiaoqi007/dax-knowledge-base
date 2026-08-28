---
title: "Pivoting Data for Speed"
url: "https://dax.tips/2019/10/16/pivoting-data-for-speed"
published: "2019-10-16"
updated: "2019-10-24"
source: "dax.tips"
---

In this article, I’m going to describe a technique you may wish to consider to help speed up your Power BI reports. This specific technique is designed to help speed up reports that show common period comparisons such as:

- This year versus last year (as difference and/or percent)
- Delta, or percentage change over the previous period
- Month over Month
- This week vs prior week
- … any period comparison

The basic idea is to take data from *rows* in your FACT tables and pivot these values into new *columns* of other rows.

Imagine you have a simple FACT table with a Date column that stretches back in time, such as the following table. You also have a requirement is to produce a visual that shows Sales per Month (or day) for a current period alongside a value from the previous year.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B1-1.png?resize=467%2C306&ssl=1)

To produce a visual in Power BI along the lines of :

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B10-1.png?resize=462%2C156&ssl=1)

You can do this pretty quickly already, and there are many ways to write DAX calculations to achieve this. You can either use one of several [DAX Time Intelligence functions](https://docs.microsoft.com/en-us/dax/time-intelligence-functions-dax) or apply a [[row-based-time-intelligence|row-based Time Intelligence]] approach; it shouldn’t be too hard.

The problem is, all these DAX or model-based techniques require the AS Engine **perform at least two scans of the underlying table**. One scan is required to identify the rows for the current period and calculate the current period value, while another independent scan is required to find rows matching the previous period.

Two scans don’t sound terrible, but imagine if your table has many billions of rows, and also uses Direct Query as the storage mode. Throw on top of this, a large number of users hitting your report at the same time. These additional scans can become the slowest element of these types of queries.

Right now in DAX, we have a concept of [[dax-fusion|Horizontal Fusion]] which attempts to optimise by reducing the number of table scans required by a query, but we don’t *yet* have Vertical Fusion. It’s coming 🙂

### The technique

The idea is to make your ETL do a little more work before loading into Power BI by pivoting values from a previous period into new columns. This transformation takes place on your FACT table.

Create an additional column in your FACT table and populate each row with the relevant value from the previous period. In the image below, you can see the FACT table now has a new column called [Sales LY].

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B2-1.png?resize=617%2C307&ssl=1)

Feel free to add as many additional columns as you need. You’ll need one per measure/period combination. The following table shows a [Sales LM] column storing a value from the previous month in the same row.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B3-1.png?resize=749%2C308&ssl=1)

Pivoting the data in this way will allow the AS Engine to perform multiple complicated period comparison calculations in a single scan.

### E.T.L. Example

The main challenge with this technique is how to perform the data pivot operation. If you use a SQL DB as your source, the transformation is reasonably straightforward, and I show an example below. I’m sure it would be reasonably easy to do in Power Query as well. However you decide to pivot the data, you need to be careful not to drop values just because there happens to be no existing row to pivot to. You may need to generate additional rows to store values from previous periods.

Say you sell a product in a previous period that you don’t sell in a current period. You don’t want to discard the value of the prior period.

Consider the following simplified 9-row data-set that covers five products going back three years.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B4-1.png?resize=464%2C361&ssl=1)

Product A has sales in 2019, but no sales for the same period in 2018. This scenario is easy. However, Product D had sales in 2018, but not in 2019. In this case, **a new line needs to be generated for Product D in 2019 as a placeholder** to store the LY value.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B5-1.png?resize=617%2C423&ssl=1)

The following script shows how you can use the UNION and GROUP BY functions in T-SQL to help ensure data integrity.

```
CREATE TABLE [#Fact Sales]
	(
	[Date]		Datetime ,
	[Product]	VarChar ,
	[Sales]		Money 
	)
GO

INSERT [#Fact Sales] SELECT '2019-10-01' , 'A' , 90
INSERT [#Fact Sales] SELECT '2019-10-01' , 'B' , 85
INSERT [#Fact Sales] SELECT '2019-10-01' , 'C' , 80

INSERT [#Fact Sales] SELECT '2018-10-01' , 'B' , 75
INSERT [#Fact Sales] SELECT '2018-10-01' , 'C' , 70
INSERT [#Fact Sales] SELECT '2018-10-01' , 'D' , 65

INSERT [#Fact Sales] SELECT '2017-10-01' , 'C' , 60
INSERT [#Fact Sales] SELECT '2017-10-01' , 'D' , 55
INSERT [#Fact Sales] SELECT '2017-10-01' , 'E' , 50


SELECT
	[Date] ,
	[Product] ,
	SUM([Sales]) AS [Sales] ,
	SUM([Sales LY]) AS [Sales LY]
FROM (
	SELECT 
		[Date] , 
		[Product] , 
		[Sales] ,
		CAST(NULL AS Money) AS [Sales LY] -- Placeholder
	FROM [#Fact Sales]

   UNION ALL 

	SELECT 
		DATEADD(YEAR,1,[Date]) AS [Date] , -- Offset by 1 period 
		[Product] , 
		CAST(NULL AS Money) AS [Sales] , -- Placeholder
		[Sales] AS [Sales LY] 
	FROM [#Fact Sales]
	WHERE
		DATEADD(YEAR,1,[Date]) <= '2019-10-01'
	) AS T
GROUP BY 
	[Date] ,
	[Product]
```

The T-SQL script for above [is here](https://1drv.ms/u/s!AtDlC2rep7a-xmcI1ZmQvylYEQB4?e=VW4BHQ).

The result of the above T-SQL script generates the following results. Ideally, you would persist these results into a physical table, or convert to a database VIEW. The original [FACT Sale] table contained nine rows; however, the result here shows two additional rows (row 4 and 8) have been added to preserve values from the previous period.

The inner derived table is a UNION ALL of two SELECT statements. The top SELECT returns current data along with a placeholder. The second SELECT applies the relevant offset to the [DATE] column using the DATEADD function and includes a blank placeholder in the [Sales] column.

The outer GROUP BY function finally collapses the result set to its final form shown below.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B6.png?resize=564%2C349&ssl=1)

Depending on the size of your FACT table and DB engine, the initial generation of this data-set might take a while. However, the logic can be embedded into a daily E.T.L. process to apply the transformation over smaller sets, e.g. daily batches.

### Pros

This approach can significantly improve query speeds for large data sets and works well for Import or Direct Query.

The technique is not just for SUM but can work just as well for other aggregations such as COUNT, MAX, MIN etc.

### Cons

Your model will be physically larger, both in terms of additional columns to store the data from previous periods AND to hold extra placeholder rows. There is also additional work to be completed during the E.T.L. process to perform the transformation.

### Some Numbers

The following example shows the effect the technique has when used in a query over a large table. The ‘fact MyEvents’ table has 8.3 billion rows and represents telemetry data covering two years. In this example, the ‘fact MyEvents’ table uses Direct Query as its storage mode and the back-end source is an Azure SQL DW DB scaled to 400DTU.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B7-1.png?resize=468%2C399&ssl=1)

The baseline is a DAX calculation that generates a result-set by month and displays the SUM of the [Quantity\_ThisYear] along with a period comparison value and two comparison metrics.

The unoptimised query took 28 seconds to complete and required 4 x Storage Engine Scans. Of the four scans, only two were longer than a second. One of the longer scans focused on getting “current” data, while the other scan retrieved data for the previous period.

![](https://i2.wp.com/dax.tips/wp-content/uploads/2019/10/B8-1.png?fit=1000%2C615&ssl=1)

The results of the optimised DAX calculation, which takes advantage of the pivoted rows, is shown below. The only change to the DAX is the calculation at line 6 becomes simpler. The updated query now runs in 18 seconds compared with 28 seconds (35% faster), while still generating the same result.

Please also note, there is now only a single storage engine scan in the optimised version compared with 4 in the unoptimised example.

![](https://i2.wp.com/dax.tips/wp-content/uploads/2019/10/B9-1.png?fit=1000%2C645&ssl=1)

\*\* 18 seconds may still sound slow, but you usually wouldn’t be running this type of query over an 8.3 billion row table in Direct Query. This example has been used to help show the effect of this technique. Aggregation tables would bring this down to milliseconds.

### Summary

I’ve seen this work exceptionally well for tables that are hundreds of billions of rows in size. If you have large tables and you have reports that show period comparison/index calculations, this is well worth considering and worth the extra work in the E.T.L process.

Likewise, if you have a super common period comparison that turns up time and time again in your reports – this technique will probably be faster than using inbuilt DAX Time Intelligence functions. I wouldn’t recommend you apply this to every type of period comparison, just the one or two that will make a difference to most of your models.

This technique, when combined with other data modelling tricks, can enable you to perform super-complex analytics over huge data sets, all inside Power BI.

In the meantime, I will continue to push for a form of vertical fusion to be added to the engine. 🙂

Let me know what you think.

5
2
votes

Article Rating
