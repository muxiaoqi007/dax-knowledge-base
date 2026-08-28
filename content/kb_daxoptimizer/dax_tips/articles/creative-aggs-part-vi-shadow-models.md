---
title: "Creative Aggs Part VI : Shadow Models"
url: "https://dax.tips/2019/11/15/creative-aggs-part-vi-shadow-models"
published: "2019-11-14"
updated: "2019-11-14"
source: "dax.tips"
---

This post is the last in this series (for now) and describes a technique used to enhance performance in Power BI models that use [Aggregate Awareness](https://docs.microsoft.com/en-us/power-bi/desktop-aggregations). I hope you’ve enjoyed the series as much as I have writing and sharing. I will no doubt add more articles in the future but need to pause this series now as I have some non-aggregate related posts I’m keen to publish.

#### The series so far

- [[creative-aggs-part-i-introduction|Introduction to Aggs]]
- [[creative-aggs-part-ii-horizontal-aggs|Horizontal Aggs]]
  - Mixing Agg and base table to produce a single value
- [[creative-aggs-part-iii-accordion-aggs|Accordion Aggs]]
  - Variable grain aggs in a single table for extra flexibility
- [[creative-aggs-part-iv-filtered-aggs|Filtered Aggs]]
  - Using Composite Models to keep recent data in memory, and older data in the source to optimise the model size
- [[creative-aggs-part-v-incremental-aggs|Incremental Aggs]]
  - A smart way to update Agg tables to optimise refresh time
- Shadow Models (This article)
  - Create manual DUAL mode tables to work around some current limitations of Agg Awareness

### Shadow Models

A shadow model is where you add an aggregate table to your model specifically to work around some *current* limitations in the [Aggregate Awareness](https://docs.microsoft.com/en-us/power-bi/desktop-aggregations) feature. The restrictions are:

- You cannot define an aggregate table over a table that uses Import as the storage mode. An aggregate table must get defined as an alternative of a Direct Query table.
- Higher Grain aggregate tables that use the GROUP BY method to relate to dimension tables, forcing the Dimension table to use Direct Query as the storage mode. This restriction reduces the load performance of simple slicers over columns from the dimension table

Shadow Models and Aggregate Awareness are companion features to Composite Models, which is only available in Power BI.

The main issue the shadow model technique is trying to address is the fact that any table used as the target of Aggregate Awareness must use Direct Query as the storage mode. The shadow model technique manually enables a Dual storage mode over your DQ table by creating a 1:1 copy of a DQ table in import mode.

Typically, aggregate tables defined using Aggregate Awareness are smaller, summarised versions of a larger FACT table. Shadow tables are not summarised versions; rather, they have the same number of rows of the table they are shadowing.

### Agg Awareness over Import tables

Let’s look at the first scenario, which is how to use a shadow model to get around the limitation of creating an Agg table over another imported table.

The diagram below shows a basic version of the shadow model technique.

- Table (3) is a Direct Query table that has four rows of data. This table represents our FACT table in our model.
  - A relationship gets defined between the Date column in this table and the Date column in the Date dim.
  - A relationship gets defined between the Product column in this table and the Product column in the Product dim.
- Table (1) is a summarised version of Table (3) and gets imported into the model. This table has only a single row
  - A relationship gets defined between the Date column this table and the Date column in the Date dim.
  - The [Quantity] column in Table (1) gets defined as a SUM OF alternative over the [Quantity] column in Table (3) using the Manage Aggregation dialog window for Table (1).
- Table (2) in this diagram is the shadow model table. It has the same number of rows and columns as Table (3) but uses Import as the storage mode.
  - Table (2) has the same relationships to the dim tables that Table (3) has.
  - The [Quantity] column in Table (2) gets defined as a SUM OF alternative over the [Quantity] column in Table (3) using the Manage Aggregation dialog window for Table (2). The precedence setting should be a lower value than set in Table (1)

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/11/B2-2.png?resize=474%2C418&ssl=1)

A simple Shadow model.

When configured, the shadow model (Table 2) effectively turns the original direct query fact table (Table 2) into a Dual-mode table.

When the engine receives a query that tries to perform a calculation by date against the DQ table such as:

```
SUM( 'Table 3'[Quantity] )
```

Aggregation Awareness kicks in and internally rewrites the query so it can use the smaller (and faster) ‘Table 1’.

However, if a query also needs to slice by Product, the classic ‘Table 1’ agg table can’t be used. In contrast, aggregate awareness can still internally re-write the query so the faster imported ‘Table 2’ can get used.

As long as imported ‘Table 2’ has the same rows, columns and relationships as direct query ‘Table 3’, agg awareness ensures queries will never escape to the direct query table. In a roundabout way, the imported version of ‘Table 1’, is an aggregate table over another imported table, ‘Table 2’ – even though the mapping rules do not directly link the two tables.

### Higher grain shadow models

Another scenario where you might use shadow models is to help improve performance is when you have [aggregates based on group by](https://docs.microsoft.com/en-us/power-bi/desktop-aggregations#aggregations-based-on-group-by-columns) columns in your model.

A typical example of this is where you have a standard calendar dimension table in your model that has one row per day. Then, over the top of a FACT table, you have the following two aggregate tables.

- Aggregate table summarised by day.
- Aggregate table summarised by month.

In this example, the aggregate table summarised by day is easy. Because it shares the same grain as the Calendar dimension table, it can use a standard relationship.

However, the monthly aggregate table cannot have a relationship defined to the Calendar dimension table because aggregate awareness requires a strong relationship (1 to many). In this case, the aggregate table summarised by month would try to create a many to many relationship to the Calendar dimension table, and this does not work with aggregate awareness.

In this case, the monthly aggregate table can use the GROUP BY method of mapping columns to specify the [month] column in the agg table is an alternative of the appropriate [month] column in the Calendar table.

This adjustment works and means queries sliced by [month] in the Calendar table can be internally rewritten to use the smaller monthly agg table.

A side-effect of using the GROUP BY method is it forces the target table into Direct Query mode. In this case, the Calendar dimension table now needs to be changed from being Dual to use Direct Query storage mode.

This change means any slicer using a column from the Calendar Table is now forced to use DQ to populate data each time a report is loaded. The daily aggregate table can no longer use a relationship to the Calendar table and now must also use the GROUP BY method. This change means columns other than those defined using GROUP BY cannot be re-written to handle the daily aggregation table. If you need agg awareness to rewrite other columns in the Calendar table to use the daily agg, these columns need to be included in the daily agg table and configured to use the GROUP BY directive.

The issue of slow slicer load performance can get resolved by the use of a shadow table over the Calendar table.

Consider the following model showing six tables. This model has the following properties.

- Table 1: **‘Fact Sale’** Direct Query FACT table has relationships to Dimension City and Dimension Date.
- Table 2: **‘Fact Sale Agg City Date’**, is an Imported agg table with a relationship to Dimension City
- Table 3: **‘Fact Sale Agg City Month’**, is an Imported agg table with a relationship to Dimension City
- Table 4: **‘Dimension City’**, is a Dual mode dimension table
- Table 5: **‘Dimension Date**‘, is a DQ mode table
- Table 6: **‘Dimension Date (Shadow)’** is an imported table that serves as a shadow model table over table 5.
- There are no relationships defined between either agg table and the ‘Dimension Date’ Table 6.

![](https://i2.wp.com/dax.tips/wp-content/uploads/2019/11/B3.png?fit=1000%2C820&ssl=1)

Example model using a higher grain agg table

The following diagram shows how to configure the daily aggregation table. Note that the [City Key] column does not need defining because it can use the relationship defined in the model.

![](https://i2.wp.com/dax.tips/wp-content/uploads/2019/11/B4.png?fit=1000%2C753&ssl=1)

Manage Aggregation dialog for ‘Fact Sale AGG Cty Date’ Table 2

The following diagram shows how to configure the monthly aggregate table.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/11/B5.png?fit=1000%2C753&ssl=1)

Manage Aggregation dialog for ‘Fact Sale AGG Cty **Month**‘ Table 3

Finally, the following diagram shows how to configure the shadow table over ‘Dimension Date’. Note here that all columns need to be mapped – or at least columns you are likely to use for slicers.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/11/B6.png?fit=1000%2C753&ssl=1)

Manage Aggregation dialog for ‘Dimension Date (Shadow)’ Table 6

Once configured, aggregate awareness will kick in and rewrite queries when sliced by any column from ‘Dimension City’, but only the ‘MonthID’ and ‘Date’ columns from ‘Dimension Date’.

The PBIX file used to create these screenshots can be [downloaded here.](https://1drv.ms/u/s!AtDlC2rep7a-yCGzjEIY3YTt05bL?e=8aelIj)

### Summary

The shadow model technique falls firmly and squarely into the hack category. It provides a performance workaround to enable agg awareness over import tables. To a lesser degree, it can help solve some performance issues when it comes to slicers with a higher grain.

At some point in the future AS will provide the ability to create agg tables over other imported tables, and the technique will no longer be required. Until then, we have shadow models.

As always, please feel free to reach out with any questions or comments. I’d love to hear if you apply this to your models – so, at the very least, I can keep pushing for the AS engine to be enhanced so this is no longer required.

4.9
7
votes

Article Rating
