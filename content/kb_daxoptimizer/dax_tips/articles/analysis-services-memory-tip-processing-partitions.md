---
title: "Analysis Services Memory Tip (processing partitions)"
url: "https://dax.tips/2019/07/29/analysis-services-memory-tip-processing-partitions"
published: "2019-07-28"
updated: "2019-07-28"
source: "dax.tips"
---

### The Tip:

Convert calculated columns in your model to physical columns (where practical).

![](https://i2.wp.com/dax.tips/wp-content/uploads/2019/07/shutterstock_459848866.jpg?fit=1000%2C667&ssl=1)

### The Detail

If you encounter memory issues when processing partitions in an Analysis Services (AS) database, one option you might consider to resolve this is to convert calculated columns in the offending tables to physical columns.

When processing partitions, the AS engine reads in raw data from a source system. Once data is ingested, the DAX expression assigned to the calculated column is evaluated to generate values for each cell in the calculated columns kicks off. This processing happens during the re-calc phase.

Most of the time, the DAX expression used by the calculated column is trying to generate a simple value based on other information in the same row e.g.

```
Total = Price x Quantity
```

Part of the recalc work involves checking data from other, already processed partitions, to help manage HASH dictionaries etc. and this can consume more memory that is otherwise needed.

### Action

So, to save the Analysis Services database time (and memory), push the calculations from the calculated columns up into the source system.

If your source is a relational database system such as MS-SQL, this is often easily achieved by modifying the T-SQL statement (or VIEW) to incorporate the new column.

An alternative is to embed the logic into Power Query so that values for the new column get generated before Analysis Services sees them.

**Moving these calculations upstream reduces the amount of memory required by AS when loading data.**

### Power BI

This tip is also valid for Power BI based models. While you might not encounter this so much, converting calculated columns to physical columns (where practical), should improve the performance of refreshing data.

### Summary

If you are looking to optimise the amount of memory in your capacity and reduce the chance of running out during processing, there are minimal downsides to considering this “Best Practice” approach.

4.5
2
votes

Article Rating
