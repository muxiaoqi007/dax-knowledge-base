---
title: "Mixed Grain aggregations"
url: "https://dax.tips/2019/11/18/mixed-grain-aggregations"
published: "2019-11-18"
source: "dax.tips"
---

My [[creative-aggs-part-vi-shadow-models|last article]] described the Shadow Model data modelling technique and how you can use it to help resolve a specific performance challenge caused by tables forced to direct query mode in particular scenarios. The approach detailed in that article used the GROUP BY method of mapping “alternate of” columns which can result in unwanted behaviour.

In this article, I wanted to share an alternative approach, which in some ways does a better job at mixed grain aggregations.

Mixed grain aggregations are tables summarised by a grain other than the key of existing dimension tables in your model, eg. A table summarised by Month, when your Calendar table is keyed by day.

When Aggregation Awareness gets enabled in a Power BI composite model, the engine will attempt to rewrite DAX queries to use smaller, faster aggregate tables. For aggregate awareness mapping to succeed, aggregate tables need to either have a strong relationship to the tables with columns used as a slicer or in a visual or use the GROUP BY method.

Each method has pros and cons so are suited to different scenarios which I have listed in the table below.

| Mapping Method | Pros | Cons |
| --- | --- | --- |
| Strong Relationship | Can slice by any column from a related table (on one side) | The aggregate tables must be summarised by the same grain as related table |
| GROUP BY | Can slice using a column at a higher grain than the key of the related table | Can only slice by column specified in the GROUP BY directive |

The Strong Relationship method is the easiest to set up. It provides the most flexibility, but this approach forces aggregation tables to get summarised to the same grain as the key in the dimension table. When this method gest used, a visual or slicer can use any column listed in the aggregate table. This approach is especially useful when you have a high number of columns in the dimension table, and the model is going to get used for ad-hoc workloads, so you cannot predict ahead of time which columns will get included in reports. Ideally, you want users to have the ability to use any column from a dimension table and still reap the benefits of aggregate awareness. This benefit includes new columns added to the model in the future.

The GROUP BY method requires more configuration by the model author but allows an aggregation table to get connected to another table at a higher grain. When using the GROUP BY method, the only columns from the dimension table that can be used and still have aggregate awareness successfully rewrite the query are the columns with specific GROUP BY directives configured in the Manage Aggregation dialog. Multiple columns can get configured, but this forces the aggregate table also to carry these columns – which starts to eat into the advantages provided by using smaller summary tables.

The example demonstrated in this article (and the previous article) focuses on how to enable Aggregate Awareness to successfully rewrite queries when using Calendar Month as the grain.

The previous article showed how this could be solved using the Shadow Model technique. This article describes an alternative version that only uses Strong Relationships.

The following image shows how you can configure a model to allow an end-user to slice by MonthID and take advantage of the sales table aggregated by month, but still, have the flexibility to use any column from the Calendar table.

- Table 1. Fact table (Direct Query).
- Table 2. Aggregate table summarised by Date (import)
- Table 3. Aggregate table summarised by Month (import)
- Table 4. Standard Dimension table (Dual) with a strong relationship to Table 1, 2 & 3
- Table 5. Calendar Table at day level (Dual)
- Table 6. Calendar Table at month level (Dual) with a strong relationship to Table 5

![](https://i2.wp.com/dax.tips/wp-content/uploads/2019/11/B4-1.png?fit=1000%2C854&ssl=1)

Once the model gets configured, the following queries can run.

#### Query 1

```dax
EVALUATE 
	SUMMARIZECOLUMNS(
		 'Dimension Date_Month'[Calendar Year]
		,'Dimension City'[State Province]
		,"Sum of Quantity" , sum('Fact Sale'[Quantity])
		)

```

The first query is grouped by the [Calendar Year] column from table 6 along with a column from table 4. In this case, aggregate awareness successfully rewrites the query to make use of the monthly aggregate table. The query can use more than just the MonthID column from the dimension table. Any column from table 6 can get used, and aggregation awareness will still work.

#### Query 2

```dax
EVALUATE
	SUMMARIZECOLUMNS(
		 'Dimension Date_Month'[Calendar Year]
		,'Dimension Date'[date]
		,'Dimension Date'[ISO Week Number]
		,'Dimension City'[State Province]
		,"Sum of Quantity" , sum('Fact Sale'[Quantity])
		)

```

The second query is extended to now include some additional columns from table 5. Aggregate awareness successfully rewrites this query to use the daily aggregate table.

### Drawbacks

The main drawback to this approach is columns that naturally reside together in the same table, are now split across multiple tables. This separation of columns can potentially confuse users not used to seeing columns split this way. If most of the reports using a model configured this way are canned reports, this shouldn’t be too much of an issue. Whereas, if the model will get used for self-service or ad-hoc workloads, then some

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/11/B5-1.png?fit=1000%2C748&ssl=1)

As you can see in the image above. The visual carries [Calendar Year] and [Date] columns that a user might normally expect to find in the same table.

A good naming convention for the tables will help keep them together in the field panels.

### Summary

I mostly wanted to follow up my last article with an alternative approach you may want to consider. The techniques are not restricted to working with time dimensions and would work equally well with other dimensions – so long as you ensure data integrity.

The files used for this model are:

[Mixed Grain Agg Awareness.PBIX](https://1drv.ms/u/s!AtDlC2rep7a-yC9w23nbEkwoewh4?e=YfcYFa)  
  
[WideWorldImportersDW – Phil.7z](https://1drv.ms/u/s!AtDlC2rep7a-yC7x7y9TvopyLLrW?e=ndUpj3) << SQL backup file to be restored to <localhost> instance.

As always, please feel free to reach out if you have questions or feedback.

5
2
votes

Article Rating
