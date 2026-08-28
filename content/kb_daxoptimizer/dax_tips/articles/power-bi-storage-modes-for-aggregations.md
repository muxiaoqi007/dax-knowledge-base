---
title: "Power BI Storage Modes for Aggregations"
url: "https://dax.tips/2021/10/11/power-bi-storage-modes-for-aggregations"
published: "2021-10-10"
updated: "2021-10-12"
source: "dax.tips"
---

## Introduction

How to choose the correct storage mode for Power BI Tables.

This article aims to help explain the different storage modes available when designing an aggregation strategy for a Power BI Report. What each storage mode is and when you would use it. Picking the correct storage mode for each table in your model can significantly affect overall performance.

The quickfire summary to take away from this article is to use the following storage modes:

| Table function | Recommended Storage Mode |
| --- | --- |
| Large Fact Table | Direct Query |
| Dimension Tables | Dual (NOT IMPORT!!) |
| Aggregation Table | Import (or Direct Query) |

The other articles in this series are as follows:

- [[intro-to-power-bi-aggregations|Intro to Power BI Aggregations]]
- [[power-bi-multiple-aggregations-tables|Multiple aggregations tables]]
- DUAL storage mode DIM tables
- Higher grain aggregation tables
- GROUP BY notation
- MIN, MAX, AVG and aggregations other than SUM
- Distinct Count solutions
- Monitoring your aggregation strategy
- Power BI Automatic Aggregations
- TMSL/TOM

## Background

When Power BI first appeared over six years ago, data-modellers could choose to create either 100% Direct Query storage mode or 100% Import storage mode. What did this mean exactly? A 100% Direct query data model meant *every* table in the data model would use Direct Query as its storage mode. Likewise, a model configured as Import would mean every table in its model would import and store data inside the data model.

The two storage-mode options suit different use cases. Direct Query suits huge datasets along with near real-time scenarios. The Import storage mode provides the best performance for end-user queries.

More recently, a new feature got introduced to Power BI called [Composite Models](https://docs.microsoft.com/en-us/power-bi/transform-model/desktop-composite-models). This new feature allows a data modeller to mix and match storage modes for tables in a single model. Some tables can use Direct Query as the storage mode, while others can use the Import storage mode. A new storage mode got introduced at the same time called Dual. A table using Dual storage mode is both Direct Query and Import at the same time.

This article dives a little deeper into the three storage modes and explains when you might use each and why.

First off, let’s recap what the three different storage modes are.

## Direct Query

When Power BI visuals require data from a table configured in Direct Query (DQ) mode, data gets retrieved “on the fly” from the data source. Power BI quickly generates queries at runtime in the dialect of the data source as end-users interact with Power BI Reports. If a data source happens to be an Azure SQL DB, Power BI creates TSQL statements. If the data source is Oracle, then Power BI generates PL-SQL statements to retrieve data.

These queries get passed to the upstream data source, which returns data to Power BI to render visuals and populate slicers.

The memory footprint for a table using DQ is small since no data gets kept in the model. The last time I checked, the file size for a 100% direct query dataset was around 1MB in size.

A comprehensive matrix of data sources for Power BI is available here:

[Power BI data sources – Power BI | Microsoft Docs](https://docs.microsoft.com/en-us/power-bi/connect-data/power-bi-data-sources)

This list shows which Power BI data sources support Direct Query. Data sources that don’t support DQ require the import mode for storing data and currently cannot be used for Power BI Aggregations.

## Import

Tables in a model configured to use Import storage mode keep a copy of data inside the data model. Data gets copied from underlying data sources into the data model during refresh operations. Part of each refresh operation optimises and compresses imported data to store inside the model in memory.

A significant benefit of Import storage mode over DQ is faster end-user query speed. However, more data loaded into a model in Import tables increases the memory required to host a model. The time taken to load data into the Import table also becomes a factor and may need to be managed.

## Dual

Dual-mode tables operate as both DQ and Import at the same time. Power BI uses the DQ version of a Dual-mode table when it makes sense. Equally, Power BI will use the Import version of the same Dual-mode table when it makes sense. I cover some examples of each in the next section.

Because tables using Dual mode are also Import, the model’s size will increase as more data gets loaded into the table, and the time taken to load data can become a factor to manage.

## Recommendations

If you have read some of my earlier articles, you will see I have already made the following recommendations for which storage mode to use when working with aggregation tables.

| Function | Storage Mode |
| --- | --- |
| Fact Table | Direct Query |
| Dimension Table | Dual |
| Aggregation Table | Import (or Direct Query) |

### Fact Tables

For aggregation tables to work, the underlying Fact table needs to be in DQ mode. The user-defined aggregation feature currently requires this and will not work if a Fact table another storage mode.

Every Power BI visual using a calculation involving a column in a Fact table will use direct query unless it can use an aggregation table to get the data it needs.

It is possible to import Fact tables using the [[creative-aggs-part-vi-shadow-models|Shadow Model]] technique and still have aggregations work; however, this is an advanced technique and out of scope for this article.

### Dimension Tables

Dimension tables should always use Dual mode. There are two common query patterns generated against Dimension tables.

#### Query Pattern 1 – Slicer or Filter

The first query pattern involves getting unique values from a single column in the Dimension table to populate a slicer or filter. For example, to generate a unique list of all countries from a geography dimension, or perhaps a list of months from a date dimension.

In this case, it makes sense for Power BI to use the Import version of the Dual-mode table as there is no need to access values across relationships in other tables. Retrieving data from the import table will be faster than using the DQ version of the Dimension table.

If this type of query uses DQ mode, it still produces the correct values, albeit slower than the same query to an Import table.

#### Query Pattern 2 – Fact table grouped or filtered by Dimension table

The second query pattern is when data in a Fact table gets grouped or filtered using data in a dimension table. An example of this pattern is when the SalesAmount column in a Fact table gets aggregated using a SUM() function while grouped by a column in a related dimension table.

In this case, Power BI creates a query expression including join and grouping logic for both the dimension and fact table. The data source can apply the grouping logic and return only the data required to render the visual to Power BI. The Fact table is already in DQ mode, and because the Dimension table is in Dual-mode, Power BI can treat the dimension table as if it was also DQ.

If the Dimension table gets configured to use Import instead of DQ, the number and type of query expression generated by Power BI are less efficient.

## How to configure storage mode

When using a composite model, it is always good to start every table in DQ mode. S*tarting* in DQ mode, you cannot use Power BI Desktop to change an Import (or Dual) table back to Import mode. If you inadvertently switch a DQ table to Import or Dual, you will need to delete the table and re-add it using the correct storage mode.

To change the storage mode, switch Power BI Desktop to the Model view from the left-hand navigation bar. Once in the Model view, select a table and scroll to the bottom of the Properties panel. You can change the storage mode using the dropdown list in the Advanced section.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/10/B1.jpg?w=800&ssl=1)

## Example 1 – Slicer query

Consider a query plan generated to get data to populate a slicer for Calendar Year from a Date Dimension. The query only needs to get a list of unique values from a single column in the dimension table. There is no need to use data contained in a related table for this query to succeed. In TSQL, this query might look something like “SELECT DISTINCT CalendarYear FROM dimDate”.

In Power BI the actual query generated happens to be:

```dax
// DAX Query
DEFINE
  VAR __DS0Core = 
    VALUES('DimDate-Dual'[CalendarYear])

  VAR __DS0PrimaryWindowed = 
    TOPN(101, __DS0Core, 'DimDate-Dual'[CalendarYear], 1)

EVALUATE
  __DS0PrimaryWindowed

ORDER BY
  'DimDate-Dual'[CalendarYear]

```

The above DAX query returns the top 101 unique values from the [CalendarYear] column in the ‘DimDate-Dual’ table. Just one storage engine event gets generated, and in this case, the subclass of “Scan” confirms data got retrieved from the in-memory Import version of the table.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/10/B2.jpg?w=800&ssl=1)

The TSQL statement is small and straightforward.

## Example 2 – Grouping query using Dual mode Dimension

Now consider the query required to get data to plot the sum of factInternetSales[SalesAmount] grouped by values from dimDate[CalendarYear].

A simplified DAX query is as follows.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/10/B3.jpg?resize=1000%2C469&ssl=1)

The query completes in 666ms ([Matthew](https://twitter.com/SQLAllFather) would be proud) and, importantly, only generates a single DQ storage engine event. Note the Formula Engine (FE) value is only 11ms. No further work is required once the single storage engine event completes. This output is an example of a well-behaving query.

The TSQL for the storage engine event is as follows:

```
SELECT
  TOP (1000001) *
FROM
  (
    SELECT
      [ t3 ].[ CalendarYear ],
      SUM([ t1 ].[ SalesAmount ]) AS [ a0 ]
    FROM
      (
        (
          select
            <columns removed for brevity>
          from
            [ dbo ].[ FactInternetSales ] as [ $ Table ]
        ) AS [ t1 ]
        INNER JOIN (
          select
            <columns removed for brevity>
          from
            [ dbo ].[ DimDate ] as [ $ Table ]
        ) AS [ t3 ] on ([ t1 ].[ OrderDateKey ] = [ t3 ].[ DateKey ])
      )
    GROUP BY
      [ t3 ].[ CalendarYear ]
  ) AS [ MainTable ]
WHERE
  (NOT(([ a0 ] IS NULL)))
```

This query pattern is simple, clean and efficient. The dataset returned to Power BI will only contain as many rows as needed to populate the visual. DimDate table gets joined to the FactInternetSales table at line sixteen. A grouping gets applied to the CalenderYear column in the DimDate table at lines twenty-three and twenty-four. The Sum aggregation function gets applied at line seven.

## Example 3 – Grouping query using Import Dimension

Now let’s run the same query from Example 2, but this time using a model where the DimDate table uses Import storage mode rather than Dual. Everything else in the model remains unchanged.

For a start, there are now two storage engine events. The first retrieves a list of DateKey/CalendarYear pairs from the import version of the DimDate table. This first SE event returns 3,652 pairs which help build the TSQL statement for the second storage engine event.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/10/B4.jpg?resize=800%2C397&ssl=1)

The TSQL shows the DQ query generated by Power BI sent to the underlying data source is very long. The query text includes a table created with 3,652 UNION statements to generate a derived table. The alias for the derived table is [semijoin1]. The number of UNION statements matches the number of unique values from the column used in the 1-side of the join. This query is not as efficient or straightforward as the TSQL created by Example 2.

In this example, the Formula Engine (FE) shows 610ms of activity. This output shows further work gets done to the resultset once it is returned from the data source.

```
SELECT
TOP (1000001)
  *
FROM (SELECT
  [semijoin1].[c66],
  SUM([a0])
  AS [a0]
FROM (
    (
        SELECT
            [t1].[OrderDateKey] AS [c22],
            [t1].[SalesAmount] AS [a0]
        FROM 
            (
                (
                SELECT
                &lt;columns removed for brevity>
                FROM [dbo].[FactInternetSales] AS [$Table]
                )
            ) AS [t1]) AS [basetable0]

        INNER JOIN (
            
            (SELECT 8 AS [c66],20100701 AS [c22] )  UNION ALL 
            (SELECT 8 AS [c66],20100702 AS [c22] )  UNION ALL 
            (SELECT 8 AS [c66],20100703 AS [c22] )  UNION ALL 
            (SELECT 8 AS [c66],20100704 AS [c22] )  UNION ALL 
            (SELECT 8 AS [c66],20100705 AS [c22] )  UNION ALL 
            (SELECT 8 AS [c66],20100706 AS [c22] )  UNION ALL 
            (SELECT 8 AS [c66],20100707 AS [c22] )  UNION ALL 
            (SELECT 8 AS [c66],20100708 AS [c22] )  UNION ALL
            ... &lt;over 3000 lines removed for brevity> ...
        
            ) AS [semijoin1]
            ON (
                ([semijoin1].[c22] = [basetable0].[c22])
                )
            )
GROUP BY 
    [semijoin1].[c66]) AS [MainTable]
WHERE (

    NOT (
            (
            [a0] IS NULL
            )
        )
)
```

Note, I didn’t copy the entire query text as it was very long. I deliberated truncated a section of the query text with a highly repetitive UNION ALL pattern. Yikes!!!

## Summary

Hopefully, these examples show why Dual mode for dimension tables make sense. If your model includes a large fact table in DQ mode, it makes sense to try and optimise any cross-table query involving grouping/filtering for the data source. Both patterns covered in this article are very common, and I am surprised how often I get involved in performance reviews where Dimension tables are still in Import mode.

While this article sits in a series on using aggregation tables in Power BI, I recommend using Dual-mode tables in any composite model. Yes, it is technically possible to build a model that doesn’t include aggregation tables. Not sure why you would 🙂

I hope this has been helpful. My next article in this series will cover mixed grain aggregation tables.

5
8
votes

Article Rating
