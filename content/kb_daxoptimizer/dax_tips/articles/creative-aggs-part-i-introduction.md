---
title: "Creative Aggs Part I : Introduction"
url: "https://dax.tips/2019/10/18/creative-aggs-part-i-introduction"
published: "2019-10-18"
updated: "2019-11-14"
source: "dax.tips"
---

### Introduction

This is part 1 in a series of articles I plan to write over in the coming weeks regarding the use of Aggregate Tables in Analysis Services databases. Some of the techniques covered in this series have been developed after working on some of the largest models around.

The series will cover :

- Introduction and basics (this article)
- [[creative-aggs-part-ii-horizontal-aggs|Horizontal Aggs]]
  - Mixing Agg and base table to produce a single value
- [[creative-aggs-part-iii-accordion-aggs|Accordion Aggs]]
  - Variable grain aggs in a single table for extra flexibility
- [[creative-aggs-part-iv-filtered-aggs|Filtered Aggs]]
  - Using Composite Models to keep recent data in memory, and older data in the source to optimise the model size
- [[creative-aggs-part-v-incremental-aggs|Incremental Aggs]]
  - A smart way to update Agg tables to optimise refresh time
- [[creative-aggs-part-vi-shadow-models|Shadow Models]]
  - Create manual DUAL mode tables to work around some current limitations of Agg Awareness

I will try to explain what Agg tables are, why and how you can use them in a variety of creative and interesting ways to resolve queries much faster than you otherwise might. I will often refer to Aggregate Tables as **Agg tables** for brevity.

Agg tables are not just for large models with billion row tables. Even if the largest table in your model only has thousands of rows, if you want your end-users to have super snappy reports, you need to add Agg tables.

Hopefully, you can use some of the techniques in this series to optimise your reports. All the techniques covered in this series can be modified or varied to suit your data model.

Please feel free to contact me to let me know how you get on or ask questions relating to anything in this series.

### Introduction

I’ve always been a big fan of using aggregate tables in Analysis Services data models, both for multi-dimension and tabular. Used properly, aggregate tables can make a world of difference in the speed and usability of the experience for end-users. Data models are getting larger and larger, and more people in organisations are using tools such as Power BI to visualise and interact with data models. The reality is, no matter how fast your underlying storage is (disk or memory), using pre-processed summary tables is the key to unlocking super-fast performance.

### What is an aggregate table?

An aggregate table is a summarised version of another table in your model, grouped at a lower grain, but still has the ability to answer *some* of the same queries that could be directed at the original table.

Consider the following **base** table that has eight rows and shows some Quantity values for a given Date and Product. I have kept this example to a small number of rows for simplicity, but the theory could (and should) easily apply to tables with millions, or billions of rows.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B1-2.png?resize=363%2C278&ssl=1)

Table 1

*Table 1* can get summarised in several ways. One version (shown below) drops the [Product] column altogether to produce a table with only two rows.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B2-3.png?resize=361%2C104&ssl=1)

Table 2

The smaller *Table 2*, also referred to as an **Agg table**, can be used in place of *Table 1* to produce the same result for a wide variety of calculations, including:

- SUM Total Quantity by Day, Week, Month, Year or All-time
- Count of Transactions by Day, Week, Month, Year or All-time
- AVERAGE Total Quantity by Day, Week, Month, Year or All-time
- Period Comparisons (Quantity or Count of Transactions)
- Percentage Change
- .. and more

The only calculations you couldn’t use *Table 2* for, would be anything required to show at a per-product level. In this example, *Table 2* was only the quarter of the size of Table 1, so DAX queries run against the smaller table are likely to run 4 x faster than similar queries run against *Table 1*.

If you extrapolate these simplified numbers to larger tables, then 10-second query could potentially be returned in 2½ seconds without any other optimisations to your model. This optimisation starts to pay dividends when your report is being used under a heavy load by many concurrent users.

Two important techniques used to help optimise Analysis Service models are 1) to reduce the number of rows scanned by the engine to produce a value and 2) to reduce the number of scans needed to take place. Aggregation tables primarily help with 1), but can also help with 2).

Each aggregation table added to the model increases the physical size in memory and also increases the associated data refresh/processing time. However, if a good aggregation strategy is designed, it opens the door to converting your model to a [Composite Model](https://docs.microsoft.com/en-us/power-bi/desktop-composite-models) and using the Direct Query storage mode for larger base tables. This can result in much smaller (and faster) models that make better use of available capacity.

Think of aggregate tables as a performance optimisation technique, similar to the way you use indexes in SQL databases to help speed up SQL queries.

One way to think consider designing which Agg tables to create in your model is to look across all visuals in your report when it is first opened. The more visuals being loaded using Agg tables, the better the experience is going to be for your end-users. Just use the following flowchart and you can’t go wrong. 🙂

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B9.jpg?resize=383%2C381&ssl=1)

Data modelling – Phil style 🙂

Just as you can add multiple indexes tables in SQL databases, you can also add multiple aggregation tables per FACT table in your Analysis Services data model. Once aggregation tables have been added, you can help determine when each aggregate table gets used.

One way Agg tables differ from Indexes on SQL tables is the responsibility to keep data synchronised between tables is on the model author. Keeping the data in sync between the base and Agg tables is not managed automatically by the AS database engine. Using the example from earlier in this article, if a ninth record gets added to *Table 1*, it is now potentially out of sync with *Table 2*. It is up to the model author to rectify this discrepancy through a managed refresh strategy.

### How to use Aggregation Tables?

Once you have an aggregation table in your model, there are two methods to take advantage of them in your calculations. The first is to add DAX conditional flow control logic functions such as [IF](https://docs.microsoft.com/en-us/dax/if-function-dax) and [SWITCH](https://docs.microsoft.com/en-us/dax/switch-function-dax) inside measures to test for specific conditions or filters. Then, depending on the outcome, explicitly state which agg or base table should be used by the calculation.

The other method available is to take advantage of the [Aggregate Awareness](https://docs.microsoft.com/en-us/power-bi/desktop-aggregations) feature in Power BI models to allow the query engine to rewrite your query to use an aggregate table automatically. Aggregate Awareness is a fantastic feature and certainly makes it easier to use aggregation tables, but there are some limitations that I will cover later in this series (including some handy workarounds 🙂 ).

At the time of writing this blog, both the Composite Models and [Aggregate Awareness](https://docs.microsoft.com/en-us/power-bi/desktop-aggregations) features are only available for Power BI models. These features are not available in SSAS, Azure AS or Excel Power Pivot. You can still make full use of aggregation tables in these versions of AS; however, you will need to use explicit DAX to determine when and how Agg tables are used.

#### Explicit DAX

This approach requires the model author to include additional conditional logic to DAX calculations to control if an Agg or base table gets used. Using data from Table 1 and Table 2, the following two measures show an example of explicit DAX.

```
Sum of Quantity = 
    IF (
	ISINSCOPE('Table 1'[Product]),
	//Then
	SUM('Table 1'[Quantity]) ,  // use base table
	//ELSE 
	SUM('Table 2'[Quantity]) * 10000 // use agg table
	)
```

and

```
Count of Transactions = 
    IF (
	ISINSCOPE('Table 1'[Product]),
	//Then
	COUNTROWS('Table 1') ,  // use base table
	//ELSE 
	SUM('Table 2'[Count of]) * 10000 // use agg table
	)
```

both calculations include a multiplier of 10,000 to line 7, so it is obvious in the visuals below in *Figure 1* if an agg or base table is being used to generate the value.

An example PBIX File that includes these measures can get [downloaded here](https://1drv.ms/u/s!AtDlC2rep7a-xnInqkZGqrAeR3re?e=OWHVhT).

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B3-2.png?resize=971%2C580&ssl=1)

Figure 1

The only time *Table 1* base table is used in any of the visuals in Figure 1, is for values inside the red blocks in some rows of the matrix. The vast majority of all values across the visuals have been calculated using the much smaller *Table 2* agg table. Notice the sub-totals in the matrix come from the agg table in the same visual.

Both measures use an [IF](https://docs.microsoft.com/en-us/dax/if-function-dax) function at line 2 to test if a filter over the ‘Table 1′[Product] column exists in the current context. If the result of the <logical test> is true, *Table 1* gets used, but in the absence of any filter on this column, *Table 2* gets used.

These examples both use the [ISINSCOPE](https://docs.microsoft.com/en-us/dax/isinscope-function-dax) function. Other DAX functions that can also be used to help determine the correct flow control are :

- [ISFILTERED](https://docs.microsoft.com/en-us/dax/isfiltered-function-dax)
- [HASONEFILTER](https://docs.microsoft.com/en-us/dax/hasonefilter-function-dax)
- [HASONEVALUE](https://docs.microsoft.com/en-us/dax/hasonevalue-function-dax)
- [NOT](https://docs.microsoft.com/en-us/dax/not-function-dax)

This example is pretty simple because the only difference between *Table 1* and *Table 2* is a single column has been removed. In reality, your model will be more complex and involve many more columns and measures to manage. To avoid repeating the same <logical test> per measure, the DAX expression to perform the test can be separated to its own calculated measure such as :

```
Table 2 Agg Test = ISINSCOPE('Table 1'[Product])
```

```
Sum of Quantity = 
    IF (
	[Table 2 Agg Test],
	//Then
	SUM('Table 1'[Quantity]) ,
	//ELSE 
	SUM('Table 2'[Quantity]) * 10000
	)
```

```
Count of Transactions = 
    IF (
	[Table 2 Agg Test],
	//Then
	COUNTROWS('Table 1') ,
	//ELSE 
	SUM('Table 2'[Count of]) * 10000
	)
```

Then the IF function inline 3 in both calculations can now reference the new [Table 2 Agg Test] measure. If the structure of either *Table 1* or *Table 2* is changed in any way, the only change required may be a small modification to the DAX in the [Table 2 Agg Test] measure to retain data integrity.

The DAX expression in the <logical test> measure is more likely to compromise a series of ISINSCOPE functions strung together by set logical operators (AND, OR, NOT) to help decide when an aggregation table can get used.

One advantage of using the Explicit DAX approach is you can use it in all flavours of Analysis Services (SSAS, Azure AS, Power BI, Excel Power Pivot); however, the downside is the additional DAX required for your calculations. But hey, DAX is fun, so it’s not really much of a downside 😉

### Aggregate Awareness

The other way to take advantage of Aggregate tables is through the use of the Aggregate Awareness feature. Both the [Composite Model](https://docs.microsoft.com/en-us/power-bi/desktop-composite-models) and [Aggregate Awareness](https://docs.microsoft.com/en-us/power-bi/desktop-aggregations) features were added to Power BI back in 2018. These features are not currently available in SSAS, Azure AS or Excel Power Pivot. Another important limitation is that Aggregate Awareness will currently only work against [data-sources that support Direct Query](https://docs.microsoft.com/en-us/power-bi/desktop-directquery-data-sources) mode.

Aggregate Awareness works inside the AS engine by attempting to detect and redirect (or internally re-write) queries aimed at larger base tables to use smaller aggregate tables. This process is automatic and does not require additional DAX to be added to your calculations.

The core concept of Aggregate Awareness is to define a set of “**Alternate Of**” mappings between columns in your Aggregation table to columns in base tables.

When used well, Agg Awareness can altogether remove the need to add Explicit DAX to your calculations and therefore greatly simplify your models.

Agg Awareness only re-directs queries if certain conditions get met. Some of these conditions are:

- The base table needs to use Direct Query or Dual storage mode (cannot be Import). Agg Awareness or VertiPaq over VertiPaq is a planned feature.
- The data type of the column from the base and Agg tables need to match exactly. Fixed Decimal columns cannot get used as an Alternative Of a Decimal column
- Strong relationships need to exist when using relationships to configure Agg Awareness.

If Agg Awareness detects a query can be satisfied by more than one aggregation table, it uses a Precedence property set on each Agg table to determine which should get used. This value is managed by the model author, and no automatic assessment takes place in the engine to determine the optimal agg table. The query will be internally rewritten to use the Agg table with the higher Precedence value.

The full Agg Awareness feature gets explained in [this blog](https://docs.microsoft.com/en-us/power-bi/desktop-aggregations), but I show below a simple example of this feature working using our simple *Table 1* and *Table 2* example tables.

The following model has three tables all set in Dual storage mode. Note, there is a strong relationship (1 to many) between the ‘Dates’ table and both the base and Agg tables.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B4-2.png?resize=665%2C447&ssl=1)

Power BI model using Agg Awareness

The Manage aggregations dialog window is opened by clicking the ellipsis in the top right-hand corner of your Agg table in the relationship view.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B5-2.png?resize=557%2C499&ssl=1)

Manage Aggregations

Once the Manage aggregations dialog opens, the columns get configured as follows. This establishes a mapping in the engine saying the [Quantity] column in the [Table 2] Agg table can be used as an **Alternate Of** the [Quantity] column from the [Table 1] base table when the SUM function gets used. If functions additional to SUM are also required, add these precalculated columns to your Agg table and map using this dialog accordingly.

![](https://i2.wp.com/dax.tips/wp-content/uploads/2019/10/B6-2.png?fit=1000%2C753&ssl=1)

Defining the **Alternate Of** mappings between base and agg tables.

Once mappings are defined and the [Apply all] button is clicked, Aggregation Awareness is now active and the following DAX calculation run. This query includes a SUM over a column in a base table along with a COUNTROWS over the same base table.

```dax
EVALUATE
	SUMMARIZECOLUMNS(
		'Dates (Dual)'[Date] ,
		"Quantity"  , SUM('Table 1 (Dual)'[Quantity]) ,
		"Count Of" , COUNTROWS('Table 1 (Dual)')
		)	

```

The following image shows the query being run in [DAX Studio](https://daxstudio.org/). The Server Timings window in the bottom right-hand corner shows a Rewrite Attempt is made and the <matchFound> tells us the rewrite was successful. This means, Aggregate Awareness has been successful, so the results shown in the bottom left-hand corner were produced from the two-row Agg table, rather than the eight-row base table.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B7-2.png?resize=837%2C368&ssl=1)

Clicking anywhere on Line 1 of the Server Timings window (inside the red box) shows more detail about the Rewrite attempt. This information can be especially useful when trying to understand why a Rewrite may have failed.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B8-2.png?resize=362%2C779&ssl=1)

Please note that in this query, a column from a third table ‘Dates’ was used to group by and the rewrite was still successful. This column was not mentioned or referenced anywhere in the **Manage Aggregations** dialog window. Agg Awareness will still successfully redirect queries to use Agg tables, even if your query includes columns from tables other than the base or agg table.

#### Aggregate Awareness using Group By

For Agg Awareness to group and/or filter by columns from tables other than the base or Agg table, a strong (1 to many) relationship must exist between itself and both the base and Agg table.

This restriction seems to imply that the grain used to summarise agg tables must always match the lowest grain (or unique column used in the 1 side of any relationship). This restriction is correct if you would like to only use relationships in your model to help Agg Awareness rewrite your queries.

However, if you create Agg tables using something other than the lowest grain of a Dimension table. Aggregate Awareness can still be configured to work by using the [Group By](https://docs.microsoft.com/en-us/power-bi/desktop-aggregations#aggregations-based-on-group-by-columns-combined-with-relationships) directive in the Manage Aggregation dialog.

A classic example is to have an Agg table summarised by Month or Year, and you would like to group and/or filter using columns from a traditional calendar table. A Year and Month column would contain duplicate values, so a strong relationship cannot be created between the Calendar table and Agg table. The only type of relationship you can create between a month (or year) column from your Calendar table and a month column in an Agg table is a direct many-to-many. Aggregation Awareness will not currently work across anything other than a strong relationship.

The [Group By](https://docs.microsoft.com/en-us/power-bi/desktop-aggregations#aggregations-based-on-group-by-columns-combined-with-relationships) mapping technique is how you can enable Aggregate Awareness to successfully rewrite DAX queries to use Agg tables when no strong relationship exists between columns in the query.

The downside of this approach is only the column specified in the Group By directive can get used in queries and not other columns from the Calendar table. Whereas, when a strong relationship exists, Agg Awareness can successfully rewrite a query using any of the columns in the table on the 1-side.

It’s possible to use a mixture of Explicit DAX and both types of Aggregate Awareness methods in a model. They are all suited to slightly different scenarios and I hope to help you understand when to use which.

### Summary

Hopefully, this article has given you a good idea of what aggregation tables are, and how they may be useful for optimising queries run against a data model. I have introduced the concept of using Explicit DAX as a method to take advantage of Agg tables, and also show how you can use the Aggregation Awareness feature built into Power BI Desktop

The next articles in this series use a mixture of Explicit DAX and the Aggregate Awareness feature to show a variety of creative ways to incorporate Agg tables in your models. When these techniques get combined with [[row-based-time-intelligence|row-based Time Intelligence]] and [[pivoting-data-for-speed|pivoting data for speed]], you should be good to create models over multi-billion row tables and still have sub-second query times in your Power BI reports.

Remember, Agg tables are not just for massive enterprise-scale models. They are a great way to speed up and optimise models of any shape and size.

5
28
votes

Article Rating
