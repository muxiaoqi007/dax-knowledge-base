---
title: "Creative Aggs Part IV : Filtered Aggs"
url: "https://dax.tips/2019/10/30/creative-aggs-part-iv-filtered-aggs"
published: "2019-10-30"
updated: "2019-11-14"
source: "dax.tips"
---

The next chapter in this series on Creative Aggs looks at Filtered Aggs, what they are, and how you might use them to speed up reports connected to Power BI and SSAS models.

The series so far :

- [[creative-aggs-part-i-introduction|Introduction to Aggs]]
- [[creative-aggs-part-ii-horizontal-aggs|Horizontal Aggs]]
  - Mixing Agg and base table to produce a single value
- [[creative-aggs-part-iii-accordion-aggs|Accordion Aggs]]
  - Variable grain aggs in a single table for extra flexibility
- [[creative-aggs-part-iv-filtered-aggs|Filtered Aggs]] (this article)
  - Using Composite Models to keep recent data in memory, and older data in the source to optimise the model size
- [[creative-aggs-part-v-incremental-aggs|Incremental Aggs]]
  - A smart way to update Agg tables to optimise refresh time
- [[creative-aggs-part-vi-shadow-models|Shadow Models]]
  - Create manual DUAL mode tables to work around some current limitations of Agg Awareness

This particular technique combines Agg Awareness and Composite models, so it is only available for models hosted in the Power BI service. This technique does not work for SSAS or AAS models.

### Filtered Aggs – the Theory

The idea behind Filtered Aggs is to reduce the amount of data imported to the model by restricting a large FACT table to only import recent data.

An example of this is a FACT table that contains data stretching back for two or more years. In this case, only a recent period, say the last three months of data gets imported into the Power BI model. The rest of the data remains in the data source and gets accessed via Direct Query.

This technique is especially useful if most reports that use the model focus on calculations over recent data.

The diagram below shows a large FACT table in red at the bottom that stretches back in time. This FACT table remains in a [Direct Query enabled data source](https://docs.microsoft.com/en-us/power-bi/desktop-directquery-data-sources). There are a series of Agg tables (shown in green) that get imported into the model. A good design would see 95% or more queries satisfied by the Agg tables.

There is a vertical **Dynamic Time Barrer** line drawn to separate the recent “hot” data to the right of the line – which gets stored in memory. The tables to the left of the barrier represent older “cooler” data,

![](https://i1.wp.com/dax.tips/wp-content/uploads/2019/10/B2-7.png?fit=1000%2C323&ssl=1)

Diagram 1. Filtered Aggs.

In the diagram, **Agg Table 1** is a particular case that while it gets configured as an aggregate table, it shares the same grain as the underlying FACT table in the data source. The **Agg Table 1** table would have the same number of rows (and columns) as the underlying FACT table has – but only for data after the time barrier. Think of this table as an imported version of the fact table *filtered* to a recent period.

Once Agg awareness gets configured, there should be no queries escaping to the direct query data source after the **dynamic time period** line. All queries covering dates to the right of this line will be satisfied by Agg Tables 1, 2 or 4.

The only time a DAX query will need to escape to a Direct Query table is when a visual needs a value from the older “cold” data that cannot be satisfied from an agg table such as Agg Tables 3 or 5.

DAX calculated measures need some explicit DAX to be added to sew together data required to satisfy criteria that may span both periods.

If implemented successfully, a core benefit would be a much smaller model in memory, leading to a shorter refresh and better overall use of server capacity.

### Dynamic Time Barrier

The Dynamic Time Barrier is a point in time that determines when “hot” tables begin and when “cold” tables end. There should be no gap (or overlap) between the end of the “cold” tables and the start of the “hot” tables. This point in time will jump forward as needed to keep the size of the model relatively stable.

The decision on when to set the time barrier needs to be made by the author of the model, and this is something that can be tuned.

|  | Pros | Cons |
| --- | --- | --- |
| Smaller Range | Smaller Model | More DQ queries |
| Longer Range | Fewer DQ queries | Larger model |

The most important thing is once a decision gets made, multiple places need to have this dynamic date defined – and it is critical these remain in sync.

An example setting is to define the dynamic time barrier to be the start of a calendar month which is three months before the current day. The following shows one way you could define this in TSQL.

#### Example WHERE clause to filter HOT tables.

```
SELECT
	*
FROM [FACT Table]
WHERE
	[Date Key] > EOMONTH(DATEADD(MONTH,-3,GETDATE()))
```

#### Example WHERE clause to filter COLD tables.

```
SELECT
	*
FROM [FACT Table]
WHERE
	[Date Key] &lt;= EOMONTH(DATEADD(MONTH,-3,GETDATE()))
```

With this technique, the boundary date will only shift when the actual date moves into a new month. The model should be configured to refresh at this point – before business hours start – if it already isn’t set to refresh daily.

### Applying Filtered Aggs in Practice

Here is a link to a [PBIX file](https://1drv.ms/u/s!AtDlC2rep7a-xzpVeyznrbDK5erN?e=WYyc8p) that uses the [WideWorldImportersDW](https://github.com/Microsoft/sql-server-samples/releases/tag/wide-world-importers-v1.0) database installed to a localhost instance.

#### Step 1 – Create DQ tables.

Create two tables in Power BI to cover the hot and cold periods of the FACT table. Both tables should use Direct Query as the storage mode and use a specific SQL query.

The TSQL used for the **Fact Sale (hot)** table is:

```
SELECT
	*
FROM FACT.Sale AS A
	INNER JOIN (
		SELECT 
			MAX([invoice date key]) AS Lastdate 
		FROM FACT.Sale
		) AS B
		ON [invoice date key] > EOMONTH(DATEADD(MONTH,-3,B.LastDate))
```

```
SELECT
	*
FROM FACT.Sale AS A
	INNER JOIN (
		SELECT 
			MAX([invoice date key]) AS Lastdate 
		FROM FACT.Sale
		) AS B
		ON [invoice date key] <= EOMONTH(DATEADD(MONTH,-3,B.LastDate))
```

The TSQL used for the **Fact Sale (cold)** table is:

#### Step 2 – Add first Agg table

Create the 1:1 hot agg table, shown in diagram 1 as Agg Table 1. This table should be set to use Import as the storage mode. The underlying SQL query should exactly match the query used for the **Fact Sale (hot)** table used in Step 1. One trick is to duplicate the query in Step 1 in the query editor, rename and then switch the storage mode for the new table to Import mode.

The data model at this point should look as follows:

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B3-6.png?resize=686%2C963&ssl=1)

#### Step 3 – Configure Agg Awareness

Configure the Agg table added at Step 2 to use Agg Awareness by connecting it to the Fact Sale (hot) table.

This example only configures the [Quantity] column for brevity, but other numeric columns can get configured. This example is the Manage Aggregations dialog for the **Agg Table 1 (hot)** table.

![](https://i2.wp.com/dax.tips/wp-content/uploads/2019/10/B4-5.png?fit=1000%2C753&ssl=1)

#### Step 4 – Add Dimensions

Add any additional dimension tables to the model. This example adds two dimension tables for brevity (Date and City). Use Direct Query as the storage mode, but update both Dimension tables to use Dual once in the model.

Create relationships between the newly added dimension tables and each of the three tables existing in the model on the appropriate column.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B5-5.png?fit=793%2C1024&ssl=1)

Ensure you use the same data source for the dimension table used for the FACT tables. One way to guarantee this is to duplicate one of the FACT tables in the query editor and edit the source to use a new query.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B6-5.png?fit=1000%2C831&ssl=1)

#### Step 5 – Create Measures

The model, at this point, represents the most basic version of this technique. Additional Agg tables should be added to ensure that as many queries as possible hitting dates before the dynamic date range do not escape to the data source.

You may also want to consider hiding both the Hot and Cold versions of the DQ tables to prevent users from querying these tables directly – without fully understanding the role each table plays in this type of structure.

Create a measure table in the model using [[a-measure-table-called-measures|this technique]].

Version 1 of a [Sum of Quantity] measure could be as simple as :

```
SUM of Quantity = 
    SUM('Fact Sale (hot)'[Quantity])
    +
    SUM('Fact Sale (cold)'[Quantity])
```

In this example, the SUM function on line 2 gets does not hit the specified DQ table; rather, it is internally rewritten to use **Agg Table 1** instead. At this point, there are no Agg tables configured as an AlternateOf Fact Sale (cold) so any queries before March 1, 2016, will escape to the data source.

A Direct Query will still be generated to the cold data even if the Date Dimension gets filtered to only show periods after the dynamic date barrier. So, to optimise this further, the following changes can be made.

A new measure can be added to store a value for the dynamic time barrier boundary. This measure is a DAX version of the WHERE clause used in the SQL in Step 1.

```dax
Dynamic Time Barrier = 
VAR myLastDate = CALCULATE(MAX('Agg Table 1 (hot)'[Invoice Date Key]),ALL())
RETURN 
    EOMONTH(DATE(YEAR(myLastDate),MONTH(myLastDate)-3,1),0)

```

The [Sum of Quantity] measure can now be updated to use the [Dynamic Time Barrier] measure as part of an IF function.

```
SUM of Quantity = 
    SUM('Fact Sale (hot)'[Quantity])
    +
    IF(
        MIN('Dimension Date'[Date]) &lt; [Dynamic Time Barrier] ,
        SUM('Fact Sale (cold)'[Quantity])
        )
```

With this update, the query will only attempt to scan cold data if the date range for the query goes before the dynamic time barrier.

#### Step 6 – Add additional Agg Tables

Additional smaller agg tables can now be added to the model using a mixture of higher grains, or dropping a grain altogether.

An example **Agg Table 2** table which covers a recent three months but drops the City grain could be as follows:

```
SELECT
	[invoice date key] ,
	SUM([Quantity]) AS Quantity
FROM FACT.Sale AS A
	INNER JOIN (
		SELECT 
			MAX([invoice date key]) AS Lastdate 
		FROM FACT.Sale
		) AS B
		ON [invoice date key] > EOMONTH(DATEADD(MONTH,-3,B.LastDate))
GROUP BY 
	[invoice date key]
```

The corresponding **Agg Table 3** table covering the cold period, is identical to **Agg Table 2**, apart from the operator used in the ON predicate.

```
SELECT
	[invoice date key] ,
	SUM([Quantity]) AS Quantity
FROM FACT.Sale AS A
	INNER JOIN (
		SELECT 
			MAX([invoice date key]) AS Lastdate 
		FROM FACT.Sale
		) AS B
		ON [invoice date key] &lt;= EOMONTH(DATEADD(MONTH,-3,B.LastDate))
GROUP BY 
	[invoice date key]
```

Both **Agg Table 2 (hot)** and **Agg Table 3 (cold)** are configured to use the Import storage mode.

The [Quantity] column in the **Agg Table 2 (hot)** table is configured as an AlternateOf the [Quantity] column in the **Fact Sale (cold)** table. Note the Precedence property is using a higher value than the aggregation configured at Step 3.

![](https://i1.wp.com/dax.tips/wp-content/uploads/2019/10/B7-5.png?fit=1000%2C753&ssl=1)

The **Agg Table 3 (cold)** table can now get configured as an AlternateOf the **Fact Sale (cold)** table.

![](https://i2.wp.com/dax.tips/wp-content/uploads/2019/10/B8-4.png?fit=1000%2C753&ssl=1)

The last thing to do is to create relationships between the two new agg tables and **Dimension Date**.

#### Step 7 – Build your report.

The model is now ready for visuals to get created in your report. An imported aggregation table should serve practically every visual. This coverage includes any variation of date selections from any column from the **Dimension Date** table.

The only time a query will escape to the Direct Query source, is when a filter gets applied to a column in the Dimension City table AND the date period stretches back before March 1, 2016.

### Summary

Filtered Aggs combines composite models and aggregate awareness nicely to provide a smart way to create smaller models that are still super fast.

Care needs to be taken to decide where to set the dynamic time range to find the best mix between model size and query performance (% of queries served by Aggs v DQ).

I’d recommend combining this with the [[pivoting-data-for-speed|Pivoting data for speed]] technique shown in a previous article.

If you think users of your model will be primarily interested in recent periods, it makes sense to prioritise this data to faster import tables – while still serving older data using aggs in most cases.

A future mod to the engine may mean there is no need to add explicit DAX to the measures to manage the periods before and after the boundary. For now we have this technique.

I have used this technique on models where the FACT table in the source had 6 billion rows stretching back multiple years. The largest table imported to the model had only 250 million rows. Every visual in the report using this model hit an agg table – and only eventually escaped to the DQ source after some serious drilling on an individual visual.

As always, I’d love to hear your feedback, so please feel free to reach out or contact me.

Next up Shadow Models.

5
12
votes

Article Rating
