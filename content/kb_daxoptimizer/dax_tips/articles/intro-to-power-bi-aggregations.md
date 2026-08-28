---
title: "Intro to Power BI Aggregations"
url: "https://dax.tips/2021/09/06/intro-to-power-bi-aggregations"
published: "2021-09-06"
updated: "2021-10-11"
source: "dax.tips"
---

This article introduces the [Power BI User-defined Aggregations](https://docs.microsoft.com/en-us/power-bi/transform-model/aggregations-advanced) feature. It includes a walkthrough example ([with video](https://youtu.be/KKWdKD4ztYQ)) showing how to configure and test a simple model to use this feature. If you have not used Power BI User-defined Aggregations before, need a refresher or reminder, or are just keen to understand how to configure, this article is for you.

The Power BI User-defined Aggregation feature is not new nor to be confused with the recently announced Automatic Aggregations, which while using the same fundamental – is covered in a later article.

This article will be the first in a series covering the following topics explaining some of the different ways to use Power BI User-defined Aggregations:

- Intro to Power BI Aggregations
- [[power-bi-multiple-aggregations-tables|Multiple aggregations tables]]
- [[power-bi-storage-modes-for-aggregations|DUAL storage mode DIM tables]]
- Higher grain aggregation tables
- GROUP BY notation
- MIN, MAX, AVG and aggregations other than SUM
- Distinct Count solutions
- Monitoring your aggregation strategy
- Power BI Automatic Aggregations
- TMSL/TOM

Links to each article will appear once I have written the article. 🙂

## Power BI User-defined Aggregations

One of the most powerful features of Power BI data modelling today is creating aggregation tables in your dataset and having simple calculations *automatically* make use of the tables without writing complex DAX. This feature is available in both Pro and Premium.

What does “automatically make use of” mean in this context?

Consider a FACT table containing millions of rows of sales data in detail in your model. You have several visuals in a report that show aggregated values summarised by month. It makes sense to create an additional table in your model summarizing the sales data in the FACT table by month, with pre-aggregated data. The new *aggregate* table can get used by your calculations to produce correct results by scanning a table with significantly fewer rows, and therefore faster query speed compared with a measure scanning the original larger FACT table.

The trick is configuring your Measures to take advantage of aggregation tables in your model, without the need to write lots of DAX IF() statements into each measure. The answer is [Power BI User-defined Aggregations](https://docs.microsoft.com/en-us/power-bi/transform-model/aggregations-advanced). Once configured, DAX calculations can be simple and do not need to be updated or changed to take advantage of the aggregation tables.

Let’s start with the good old **[AdventureWorks](https://docs.microsoft.com/en-us/sql/samples/adventureworks-install-configure?view=sql-server-ver15&tabs=ssms)** model, which has a single FACT table called **FactInternetSales**. There are two DIM tables, each with 1:M relationships to the FACT table called **DimDate** and **DimProduct**. All three tables initially use Direct Query as the storage mode. We will keep the FACT table in direct query mode but move the two DIM tables to DUAL.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/08/B1.jpg?resize=607%2C432&ssl=1)

The goal is to use an aggregation table to reduce the number of times the **FactInternetSales** table gets scanned.

## Exercise 1 – Configure Power BI Aggregations

### Step 1 – Create the aggregation table.

In my example, the data source is an Azure SQL database, so one of several options is to design a TSQL statement as the basis for the aggregation table. Power Query could just as easily produce the same query. In this example, the storage mode for the aggregation table is “Import”. The new aggregate table will summarize/group by the two foreign-key columns on the many side of each 1:M relationships used in our data model.

The two foreign-key columns used to group our aggregation are: **‘FactInternetSales'[OrderDateKey]** and **‘FactInternetSales[ProductKey].**

The TSQL statement:

```
		SELECT 
			 OrderDateKey , 
			 ProductKey ,
			 SUM(SalesAmount)	AS SalesAmount ,
			 SUM(OrderQuantity) AS OrderQuantity,
			 COUNT(*)			AS CountOfRows
		FROM dbo.FactInternetSales
		GROUP BY 
			OrderDateKey , 
			ProductKey
```

The result of the above query is a table summarised by every unique combination of product and day. In this case, the raw **FactInternetSales** table has 193 thousand rows while the aggregation table has 68 thousand rows – so it has approximately three times fewer rows.

The above TSQL statement gets used for creating a new import mode table in your dataset.

![Image showing a TSQL statement used to build an aggregation table in a simple Adventureworks model](https://i0.wp.com/dax.tips/wp-content/uploads/2021/08/B2-1.jpg?resize=494%2C393&ssl=1)

Once the new aggregation table gets loaded into the data model, rename the table to something meaningful, such as **FactInternetSales-Agg-Product/Day**. The aggregation table will not be visible to end-users, so choose a name that helps model authors quickly know the grain of the table – this is especially useful for models with multiple aggregation tables.

### Step 2 – Add measures.

The next step is to create three measures in the model. Do not create these measures on the aggregate table, as this table will not be visible to end-users.

```
Sum of Sales Amount = SUM('FactInternetSales'[SalesAmount])
```

```
Sum of Order Quantity = SUM('FactInternetSales'[OrderQuantity])
```

```
Count of Sales = COUNTROWS(FactInternetSales)
```

Notice all three DAX measures reference either a column in the **FactInternetSales** table or reference the table itself. This DAX is simple and will not need to change to use the smaller aggregate table added in step-1. We will configure the model later to know it can use the aggregate table where possible.

### Step 3 – Test which table measures are using DAX Studio

You can skip this step if you prefer, but run a quick test query to see the simple measures added in Step 2 using the raw **FactInternetSales** table rather than the newly added aggregate table.

Start a [DAX Studio](https://daxstudio.org/) session connected to the PBIX file and run the following query with a [Server Timing Trace](https://daxstudio.org/documentation/features/server-timings-trace/) started:

```dax
EVALUATE
	SUMMARIZECOLUMNS(
	
		// GROUP BY COLUMNS
		DimDate[CalendarYear] ,
		DimProduct[Color] ,
		
		// AGGREGATE CALCULATIONS
		"Sum of Sales Amount"  , [Sum of Sales Amount] ,
		"Sum of Order Quantity" , [Sum of Order Quantity],
		"Count of Transactions" , [Count of Sales]
		)

```

![Image showing a simple DAX Query in DAX Studio with a Server Timings trace running.](https://i0.wp.com/dax.tips/wp-content/uploads/2021/08/B3.jpg?resize=1000%2C665&ssl=1)

Once you have [DAX Studio](https://daxstudio.org/) opened, paste the DAX query in and start a Server Timings trace (1) from the ribbon. Once the trace is running, execute the query (2) and click the Server Timings tab at the bottom (3) to review the results.

The single row showing at (4) suggests the query scanned a Direct Query table (Subclass=SQL), confirm this by reviewing the SQL statement to see the used underlying table.

This step aims to get a before picture of what happens to queries that should use the aggregation table once it gets fully configured. Repeat this step later after the aggregation table gets configured to compare results.

### Step 4 – configure relationships.

There are two ways to configure a user-defined aggregation table in a model. This article focuses on the first method to connect the aggregate table to the relevant dimension tables using [relationships](https://daxstudio.org/). This technique requires the aggregate table to include the exact grain or level of summarisation used by each connected DIM table. The other method to configure a user-defined aggregation table is the [Aggregation based on GroupBy columns](https://docs.microsoft.com/en-us/power-bi/transform-model/aggregations-advanced#aggregation-based-on-groupby-columns), which I will cover later.

An essential advantage of the relationship technique is the aggregation table works for queries that group or filter using *any* column from each connected DIM table.

The disadvantage of this technique is, you tend to end up with larger aggregations. Tips to improve aggregation sizes get covered in later in this series.

Connect the **FactInternetSales-Agg-Product/Day** table to both the DimDate and DimProduct tables using the same columns used by the FactInternetSales table.

### Step 5 – Configure storage modes.

Ensure the DIM tables use DUAL storage mode. Do not set the DIM tables to use IMPORT storage mode. I will explain in much more detail why this is important in a later article.

Best practice rules for user-defined aggregations are:

- FACT tables covered by user-defined aggregation tables need to be DIRECT QUERY (DQ).
- Configure DIM tables with relationships to DQ FACT tables to use DUAL mode.
- Aggregation tables can be IMPORT or DQ (Import gives better query speed, DQ suits real-time requirements).

The quick reason why DIM tables should use DUAL is this storage mode creates two versions of the same table in the model. It makes an import version of the table, used by queries that only use columns in this table (no joins), such as a query to populate values for a slicer visual.

The second version of the table is a DQ version, used in queries involving joins to other DQ tables, such as the FACT table. This version of the table allows the engine to reduce the number of rows that need to back and forth between Power BI and the DQ data source to produce the correct result.

The final model should now look like this:

![Image showing the diagram view of four tables including the Manage relationship view detail](https://i0.wp.com/dax.tips/wp-content/uploads/2021/08/B4-1.jpg?resize=669%2C596&ssl=1)

### Step 6 – Map columns as alternatives.

The last step is to set the manage aggregation rules on the ****FactInternetSales-Agg-Product/Day**** table. Hence Power BI knows it can use the aggregation table as an alternative for some queries that hit the model.

Click the three-dot ellipsis in the top right of the table to access the Manage Aggregation dialog window.

![Image showing how to access the Manage Aggregation feature](https://i0.wp.com/dax.tips/wp-content/uploads/2021/08/B5-1.jpg?w=1000&ssl=1)

In the Manage aggregations dialog, configure the mappings of the aggregated columns. There is no need to configure anything on the columns used in any relationship to DIM tables. This mapping tells the model which columns in each aggregate table are available as alternatives to columns in the raw FACT table.

By configuring these rules, Power BI now knows that any query involving a SUM calculation over the OrderQuantity, or SalesAmount columns in the DQ FACT table can get automatically resolved using the smaller, faster aggregation table.

![Image showing how to configure the Manage Aggregation feature.](https://i0.wp.com/dax.tips/wp-content/uploads/2021/08/image.png?resize=697%2C547&ssl=1)

The value in the Precedence text box can be kept at zero for now. This property gets used when a model has multiple user-defined aggregation tables, and more than one is valid for a given query. The user-defined aggregation table with the higher value in this property gets priority. Ideally, tables with fewer rows should have a higher value in this property.

### Step 7 – Check queries are being mapping to the user-defined aggregation table.

Re-run the same query from step two against the updated model. If the mappings defined in the previous step get configured correctly, the output in the Server Timings tab should now show two rows. The first row has “<matchFound>” in the Query column, which means the aggregate table got used. The second row shows that a “Scan” was used instead of “SQL”, implying the storage engine operation involved a table in Import mode.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/08/image-1.png?resize=1000%2C595&ssl=1)

#### Notes:

- The new query only took a total of 10 milliseconds rather than 1.32 seconds for the original query at step 2. This speed improvement reflects that the user-defined aggregation table is three times smaller than the detail table and uses import storage mode rather than the direct query.
- The DAX calculations in the original measures did not change. Each DAX statement still references the *FactInternetSales* table.
- The DAX Query used groups by columns other than the primary key of the DIM table. Any column from the **DimDate** or **DimProduct** can get used to group or filter, and Power BI will automatically use the aggregate table.

## That’s it

The example shown here demonstrates the basic steps required to configure and test user-defined aggregations in a model. From here, whenever a Power BI Report visual generates a DAX query that *can* take advantage of the aggregate table, it will.

Adding additional columns to the aggregate table by expanding the TSQL statement (step-1), then mapping the new columns (step-5) should increase the number of times the user-defined aggregation table gets used. The more the aggregation table gets used, the better!

## General Notes and checklist reminder

- The FACT table must be in DQ mode
- Your DIM tables should be in DUAL storage mode
- The Aggregate table can be either DQ or IMPORT
- You can use 1:M relationships between common DIM tables, or use GROUP BY mapping
- You can have more than one aggregate table in the model
- Remember to keep your Imported aggregation tables in sync!!

## Summary

Adding aggregation tables to a model should significantly improve individual query times and mean much less CPU and IO are required overall. This efficiency has flow-on effects for high concurrency scenarios, and the underlying system can cope with many more users before slowing down.

You can have as many aggregation tables in your model as you like. The rule of thumb I recommend when designing an aggregation strategy is to create as many aggregation tables, so every visual in an important report page hits an aggregation table on the first load. This approach means you need to synchronize data model design to your report, which has many other benefits. Only when users of a report start to drill down on specific visuals should they get to a point where data can no longer get sourced from an aggregate table, and queries escape out to the raw detail level source.

Each Power BI visual generates a single DAX query; however, each DAX query can generate multiple Storage Engine (SE) events. Each SE event is known as a Query Shape, and it is at this level, user-defined aggregation tables get tested to see if they can be matched and used.

An important metric for any aggregation strategy is to monitor the percentage of overall DAX storage engine events that get successfully matched to an aggregation table. A higher value for this metric means users have a better experience. One way to increase this metric is to add aggregation tables.

4.9
13
votes

Article Rating
