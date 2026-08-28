---
title: "Speed up Distinct Count calculations in Power BI using Aggregations"
url: "https://dax.tips/2023/02/23/speed-up-distinct-count-calculations-in-power-bi-using-aggregations"
published: "2023-02-22"
updated: "2023-12-05"
source: "dax.tips"
---

![](https://i0.wp.com/dax.tips/wp-content/uploads/2023/02/image-10.png?resize=300%2C300&ssl=1)

This article aims to show how you can speed up **distinct count** calculations in Power BI using the built-in [user-defined aggregations](https://learn.microsoft.com/en-us/power-bi/transform-model/aggregations-advanced) feature. The user-defined aggregation feature in Power BI is designed to work with direct query models and usually gets used for calculations such as SUM, MIN, MAX etc. However, it can also work well for distinct count calculations using the pattern shown in this article.

At the time of writing, the user-defined aggregation feature allows you to create a pre-calculated aggregation table as an alternative to an often-larger fact table. Aggregation tables can use either import or direct query storage mode; however, the Fact table needs to use direct query (unless you want to get into [[creative-aggs-part-vi-shadow-models|Shadow Models]]).

Once applied, the technique is flexible enough to compute correct distinct count values while filtering and grouping by *any* column in related dimension tables.

To demonstrate, I use data from an AdventureWorksDW database to create a model over Customers, Products and Dates. Once configured, the model can use *any* combination of filtering/grouping using columns from any of these Dimensions.

The following demos walk through configuring a Power BI model to use this technique.

## Demo 1: Distinct Customers

The first demo focuses on the Customer and Date dimensions.

As a baseline, the following T-SQL query shows what we are trying to achieve using DAX against a Power BI model. The baseline query shows the correct number of unique customers for any given calendar month, e.g. there were 144 unique customers active in January 2011.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2023/02/image-1.png?resize=819%2C770&ssl=1)

```
SELECT
    D.FirstDateofMonth ,
    COUNT(DISTINCT F.CustomerKey) AS "Customers" ,
    SUM(F.SalesAmount)            AS "SalesAmount" ,
    COUNT(*)                      AS "RowCount"

FROM [dbo].[FactInternetSales] AS F

    INNER JOIN [dbo].[DimDate] AS D
        ON D.DateKey = F.OrderDateKey

GROUP BY 
    D.FirstDateofMonth

ORDER BY 
    D.FirstDateofMonth
```

### **Step 1:** Create the model.

The first step is creating a simple four-table Power BI model using AdventureWorks data.

- FactInternetSales needs to be in Direct Query mode.
- The three Dimension tables should all use Dual storage mode.
- Standard 1-to-many relationships connect FactInternetSales to the three Dimension tables as shown.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2023/02/image.png?resize=902%2C1024&ssl=1)

### Step 2: establish a baseline.

Run a DAX query against the model to check for correct results and establish a performance baseline. This baseline DAX query does not use the aggregation table and gives us a performance target to compare.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2023/02/image-3.png?resize=1000%2C944&ssl=1)

```dax
EVALUATE

	SUMMARIZECOLUMNS(
		// GROUP BY COLUMNS
		DimDate[FirstDateOfMonth] ,
		// MEASURES
		"Unique Customers" , DISTINCTCOUNT(FactInternetSales[CustomerKey]) ,
		"Sum of Sales Amount" , SUM(FactInternetSales[SalesAmount]),
		"Count of rows" , COUNTROWS(FactInternetSales)
		)
		
ORDER BY 	
	DimDate[FirstDateOfMonth]
	

```

As expected, the results of the DAX query match the baseline T-SQL query from step 1. The query took 3.4 seconds and consisted of two storage engine (SE) queries against the direct query data source.

The abbreviated version of the first SE call handled the distinct count calculation and takes 2.1 seconds:

```
SELECT TOP (1000001) 
    [t1].[FirstDateOfMonth] AS [c51],
    COUNT_BIG(DISTINCT [t0].[CustomerKey]) AS [a0]
FROM  [dbo].[FactInternetSales] AS [t0]
        INNER JOIN [dbo].[DimDate] AS [t1]
            on [t0].[OrderDateKey] = [t1].[DateKey]
GROUP BY 
    [t1].[FirstDateOfMonth]
```

The second SE call performs the non-distinct count calculations and took just 1.2 seconds:

```
SELECT TOP (1000001) 
    [t1].[FirstDateOfMonth] AS [c51],
    COUNT_BIG(*) AS [a0],
    SUM([t0].[SalesAmount]) AS [a1]
FROM  [dbo].[FactInternetSales] AS [t0]
        INNER JOIN [dbo].[DimDate] AS [t1]
            on [t0].[OrderDateKey] = [t1].[DateKey]
GROUP BY 
    [t1].[FirstDateOfMonth]
```

The results of both SE calls get sewn together in Power BI by the formula engine (FE) to produce the correct result for an overall query duration of 3.4 seconds.

### Step 3: add the aggregation table.

The next step is to create the table (or view) for the user-defined aggregation table. The following T-SQL creates an aggregation table with a unique row per Customer and OrderDate. The new aggregate table has only four columns and 113,161 rows compared with 250,430 rows in the FactInternetSales table which equates to 45.1% of the row count.

Note there is no distinct count aggregate function in this query.

```
SELECT
    OrderDateKey ,
    CustomerKey ,
    SUM(SalesAmount)    AS "SalesAmount"  ,
    COUNT(*)            AS "RowCount"
INTO factinternetsales_AGG_DCOUNT1
FROM factinternetsales
GROUP BY 
    OrderDateKey ,
    CustomerKey
```

The new aggregation table now gets added to the model. The storage mode for the new table can use either direct query or import. This example uses import, meaning Power BI processes any table calculation. If the aggregate table gets set to use direct query, the data source processes any calculation (such as distinct count). Depending on the size and capability of the direct query source, it may be preferable to keep this aggregation table in DQ mode.

Once the new table gets added to the model, create two standard 1-to-many relationships to connect the aggregation table to DimCustomer and DimDate, as shown in the diagram.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2023/02/image-4.png?resize=1000%2C771&ssl=1)

### Step 4: configure user-defined aggregations.

Open the Manage Aggregations dialogue box to configure the new aggregation table for user-defined aggregations.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2023/02/image-5.png?resize=902%2C759&ssl=1)

Each column in the new aggregation table gets configured as follows. It is essential to use GroupBy as the SUMMARIZATION option for both the CustomerKey and OrderDateKey columns (the first two rows). Notice the DETAIL TABLE for all columns gets set to use the FactInternetSales rather than the Dimension tables.

Setting the GroupBy summarization on the key columns is the magic behind this pattern. This setting combined with the relationships to the Dimension tables is what allows the design to produce an accurate distinct count result when grouping/filtering by any combination of columns in the dimension tables.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2023/02/image-6.png?resize=1000%2C788&ssl=1)

## Step 5: test results.

Once user-defined aggregations get configured, we can re-run the same DAX query from step-2 and study the results.

The query produces correct values in just 21ms. The Server Timings dialog shows two SE events against our imported aggregation table. The two SE events mirror the pattern observed against the DQ fact table in step-2. The first SE event groups by the FirstDateOfMonth column and performs a DCOUNT calculation, while the second SE event also groups by FirstDateOfMonth and handles the non-DCOUNT calculations.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2023/02/image-7.png?resize=1000%2C920&ssl=1)

The key to this pattern is the flexibility to filter or group by any column from Dimension tables related to the aggregation table. For example, the model created in Demo 1 should be able to use the user-defined aggregation table to resolve any of the following types of queries

- Number of unique customers per year
- Number of unique days for a given customer (or category of customer)

However, any attempt to filter or group using a column in DimProduct will miss the user-defined aggregation table as there is no ProductKey column in the aggregation table, nor is there a relationship between the user-defined aggregation table and DimProduct.

## Demo 2: Extending model

This example shows how to extend the model to allow filtering and grouping by columns in the DimProduct table and allow *multiple*distinct count calculations as part of the same query.

### Step 1: Add ProductKey to the aggregation table.

The definition for the previously created aggregation table (or view) in the source can be updated o have ProductKey added in both the SELECT and GROUP BY sections of the TSQL query.

The result of this change adds one new column but also increases the size of the table. The new aggregation table now has 249,006 rows compared with 250,430 in the fact table, representing 99.4% of the row size. The smaller this ratio, the better for query speed. This demo is still helpful in showing how a single user-defined aggregation table can support multiple distinct count calculations over filters/groupings from across Date, Customer *and*Product dimension tables.

An aggregation table of just OrderDateKey and ProductKey (no CustomerKey) is only 86,334 rows, or just 34.4% of the rows, compared with FactInternetSales.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2023/02/image-8.png?resize=581%2C391&ssl=1)

Once the user-defined aggregation table gets refreshed to include the new column, a standard 1-to-many relationship gets created between the aggregation table and DimProduct (on ProductKey). The Manage aggregation dialog gets updated, so the new ProductKey column has a GroupBy SUMMARIZATION over the ProductKey column in the FactInternetSales table.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2023/02/image-9.png?resize=1000%2C782&ssl=1)

## Summary

These examples show how you can use aggregation tables to speed up distinct count calculations using a smaller (and therefore) faster table to help produce correct results. All while retaining high flexibility with what columns you want to filter and group. If new columns get added to dimension tables, no additional work is required for the model to work.

The best results happen when the aggregation table is much smaller than the underlying fact table. Multiple aggregation tables in the model can help for queries requiring filtering/grouping by just one or two dimensions—the Precedence property in the Manage Aggregations dialog help here.

- Date and Customer
- Date and Product
- Customer and Product
- Date and Customer and Product

Choosing between using direct query or import as the storage mode for the aggregation table provides extra control over where the distinct count calculation will get computed.

Finally, I don’t think this is a well-known pattern, and I hope it helps anyone struggling with slow, distinct count calculations. I have a more advanced blog on another technique (using row count) to speed up distinct count planned soon. As always, please feel free to contact me via Twitter or leave a comment below.

## Video Walkthrough

Coming soon……

4.9
7
votes

Article Rating
