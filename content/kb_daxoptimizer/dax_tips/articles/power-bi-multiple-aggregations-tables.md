---
title: "Power BI Multiple Aggregations tables"
url: "https://dax.tips/2021/09/21/power-bi-multiple-aggregations-tables"
published: "2021-09-20"
updated: "2021-10-11"
source: "dax.tips"
---

Part two in this series on Power BI Aggregation tables focuses on Power BI data models with more than one aggregation table. The first article introduced Power BI Aggregations and included a walk through configuring a Power BI model with a single aggregation table.

- [[intro-to-power-bi-aggregations|Intro to Power BI Aggregations]]
- Multiple aggregations tables
- [[power-bi-storage-modes-for-aggregations|DUAL storage mode DIM tables]]
- Higher grain aggregation tables
- GROUP BY notation
- MIN, MAX, AVG and aggregations other than SUM
- Distinct Count solutions
- Monitoring your aggregation strategy
- Power BI Automatic Aggregations
- TMSL/TOM

Full video walk through.

## Race-car analogy

Why might you consider having more than one aggregation table? The short answer is speed and overall resource efficiency.

Consider a heavily used Power BI report where page-load time is considered critical. A typical report may have half a dozen visuals on a page showing values computed over various grains. If the model used by the report has no aggregation tables, all calculations use the raw fact tables to produce values for each metric.

Adding an aggregation table to the model allows the same calculations as before to use smaller tables to produce the same result. Calculations using smaller aggregation tables will enable the server hosting the data model to use much less effort per query.

Another way to think of Aggregation tables in a data model is like gears in a race car. A data model with no aggregation tables is like a race car only ever driven in first gear. Not the most efficient use of the engine! Sure, you can drive a car around a race track in first gear, and you will eventually reach the finish line. Adding an aggregation table to the data model is like moving into second gear. Adding additional, smaller aggregation tables is like adding more gears allowing the car to get to the finish line faster and more efficiently.

## Aggregation numbers

The following table shows row counts for both the raw **FactInternetSales** table and proposed aggregation tables for an [AdventureWorksDW](https://docs.microsoft.com/en-us/sql/samples/adventureworks-install-configure?view=sql-server-ver15&tabs=ssms) database. Each aggregation table includes a column with pre-computed values for the sum of SalesAmount and OrderQuantity.

| Table Name | Aggregated By | Number of Rows | % of Fact table |
| --- | --- | --- | --- |
| FactInternetSales | — | 193,281 | — |
| Agg Table 1 | Product & Date | 68,853 | 35.6% |
| Agg Table 2 | Date | 3,348 | 1.7% |
| Agg Table 3 | Product | 131 | 0.1% |

Consider a visual that only needs to show the total SalesAmount for all products and time. This metric could compute accurately from any of the four tables in our set of tables. If a query uses the FactInternetSales table, it needs to scan 193,281 rows of data and perform more CPU and IO operations than using any of the smaller aggregation tables. The best result will be if the query uses **Agg Table 3** with only 131 rows of data.

Now consider a similar visual showing SalesAmount for all products by day, month or year – that includes a period comparison metric to show the percentage change. In this case, only **Agg Table 3** is not valid, while the remaining tables can produce the correct result. Ideally, the query automatically uses Agg Table 2 because it is still a tiny fraction of the size compared with **Agg Table 1** and **FactInternateSales**.

Finally, if the visual needs to show the sum of SalesAmount by Product Category *and* Month, the query can still use **Agg Table 1** and only scan one-third of the number of rows to produce the correct result.

## How to configure

Now that we have covered why you might add multiple aggregation tables and some of the benefits, let’s consider configuring a model. The important thing is to ensure the most efficient aggregation table gets used when there are multiple options.

As per the previous article in this series, this exercise with a simple Adventureworks model. The model has a single FACT table called **FactInternetSales**. There are two DIM tables, each with 1:M relationships to the FACT table called **DimDate** and **DimProduct**. All three tables initially use Direct Query as the storage mode. We will keep the FACT table in direct query mode but move the two DIM tables to DUAL.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/08/B1.jpg?resize=607%2C432&ssl=1)

The exercise is to add three aggregation tables to the model and configure each to be

### Step 1 – Create three aggregation tables.

The source data for this example lives in an Azure SQL database, so the following TSQL statements are used to generate three aggregation tables to get added to the model. Create three new tables in the data model using IMPORT storage mode.

#### FactInternetSales-Agg-Product/Day

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

#### FactInternetSales-Agg-Day

```
		SELECT 
			 OrderDateKey , 
			 SUM(SalesAmount)	AS SalesAmount ,
			 SUM(OrderQuantity) AS OrderQuantity,
			 COUNT(*)			AS CountOfRows
		FROM dbo.FactInternetSales
		GROUP BY 
			OrderDateKey
```

#### FactInternetSales-Agg-Product/Day

```
		SELECT 
			 ProductKey ,
			 SUM(SalesAmount)	AS SalesAmount ,
			 SUM(OrderQuantity) AS OrderQuantity,
			 COUNT(*)			AS CountOfRows
		FROM dbo.FactInternetSales
		GROUP BY 
			ProductKey
```

### Step 2 – Configure relationships.

Once three new aggregation tables get added to the model, configure relationships as follows:

The **FactInternetSales-Agg-Product/Day** table should have a relationship to both the DimDate and DimProduct tables.

The **FactInternetSales-Agg-Day** table should have a relationship to just the DimDate table.

The **FactInternetSales-Agg-Product** table should have a relationship to just the DimProduct table.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/09/B2.jpg?resize=562%2C398&ssl=1)

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/09/B1.jpg?resize=627%2C599&ssl=1)

## Step 3 – Configure Aggregations

Once the three aggregation tables get added to the model, each table must get configured as an aggregation table. This simple action allows the model to understand these new tables can get used as alternatives to queries against the underlying **FactInternetSales** table. For each table, open the **Manage aggregations** dialog to configure.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/08/B5-1.jpg?resize=708%2C617&ssl=1)

### FactInternetSales-Agg-Product/Day

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/09/B3.jpg?w=800&ssl=1)

### FactInternetSales-Agg-Day

Note, for the **FactInternetSales-Agg-Day** table, a value greater than zero needs to be set in the precedence property. In this case, I have used 10. This value needs to be numeric and used as the tie-breaker to decide which aggregation table gets used when more than one aggregation table can get used for a query.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/09/B4.jpg?w=800&ssl=1)

### FactInternetSales-Agg-Product

Finally, configure the third aggregation table as follows. This aggregation table will use a value of 20 for the precedence property.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/09/B5.jpg?w=800&ssl=1)

## Step 4 – Test aggregations

Once the three aggregation tables are configured, run the following query in an instance of DAX Studio connected to Power BI Desktop. The first query performs a sum over the **SalesAmount** column from the **FactInternetSales** table. The query does not filter or group in any way, and could be satisfied by any aggregation table.

Be sure to start a Server Timings trace by clicking the Server Timings button on the ribbon before executing the following query.

### Test Query 1

```dax
EVALUATE
	{ SUM(FactInternetSales[SalesAmount]) }

```

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/09/B6.jpg?resize=1000%2C719&ssl=1)


| Table Name | Is valid for Query | Agg Precedence | Table used |
| --- | --- | --- | --- |
| FactInternetSales | Yes | — | No |
| FactInternetSales-Agg-Product/Day | Yes | 0 | No |
| FactInternetSales-Agg-Day | Yes | 10 | No |
| FactInternetSales-Agg-Product | Yes | 20 | YES |

Line 1 of the Server Timings tab shows a **RewriteAttempt** and a <matchFound>. The term <matchFound> confirms an aggregation table got used. The right-hand window of the Server Timings tab gives good detail on which aggregation table gets used. The Details section of the Aggregation Rewrite Attempt window shows additional information explaining why an expected aggregation table was matched or missed.

### Test Query 2

The next query used to test the aggregation tables, groups by a column in the **DimDate** table. By grouping in a column in the **DimDate** table, the smaller **FactInternetSales-Agg-Product** table is not used by the model to resolve the query. This query shows the RewriteAttempt scan is successful, and the **FactInternetSales-Agg-Day** uses the alternative to the FactInternetSales table.

The **FactInternetSales-Agg-Day** and **FactInternetSales-Agg-Product/Day** table are valid alternatives, so the precedence property determines which table wins the tie.

```dax
EVALUATE
	SUMMARIZECOLUMNS(
		DimDate[CalendarYear] ,
		"Sum of SalesAmount" , SUM(FactInternetSales[SalesAmount])
		)

```

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/09/B7-1.jpg?w=800&ssl=1)

| Table Name | Is valid for Query | Agg Precedence | Table used |
| --- | --- | --- | --- |
| FactInternetSales | Yes | — | No |
| FactInternetSales-Agg-Product/Day | Yes | 0 | No |
| FactInternetSales-Agg-Day | Yes | 10 | YES |
| FactInternetSales-Agg-Product | NO | 20 | No |

### Test Query 3

The third and final query performs the same sum calculation over a column in the FactInternetSales table, but this time groups by columns in the **DimProduct** and **DimDate** tables. In this case, only one of the three aggregation tables is now valid for the combination of columns in the query. The DAX Studio Server Timings image shows the FactInternetSales-Agg-Product/Day table successfully used to resolve the query.

```dax
EVALUATE
	SUMMARIZECOLUMNS(
		DimDate[CalendarYear] ,
		DimProduct[Color],
		"Sum of SalesAmount" , SUM(FactInternetSales[SalesAmount])
		)

```

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/09/B8.jpg?w=800&ssl=1)

| Table Name | Is valid for Query | Agg Precedence | Table used |
| --- | --- | --- | --- |
| FactInternetSales | Yes | — | No |
| FactInternetSales-Agg-Product/Day | Yes | 0 | YES |
| FactInternetSales-Agg-Day | No | 10 | No |
| FactInternetSales-Agg-Product | No | 20 | No |

## Summary and notes

And that’s it! This article walks you through showing a simple implementation of multiple aggregation tables in a Power BI data model. We also looked at how you can check to see if queries match aggregation tables once configured.

The examples shown here use a relatively small Fact table with only 193k rows. The performance gain by using aggregation tables may not seem worth the effort, particularly in single-user mode. The real benefits of aggregation tables kick in when your Fact tables grow much larger along with the number of concurrent users.

The main point of this article shows you do not need to stop with just one aggregation table. It should be easy and quick to add additional tables to your model. How many aggregation tables are too many? My rule of thumb is to add as many aggregation tables as required to ensure every visual on a crucial report page loads from an aggregation table on the first load. When users start to drill down on individual visuals should generate queries that cannot match agg tables and *escape* to the raw Fact table.

Be aware that role-playing dimensions such as a **DimDate** table that can join to a fact table on multiple columns need special care. Only aggregation tables with joins to the **DimDate** table get considered by the aggregation matching mechanism.

5
7
votes

Article Rating
