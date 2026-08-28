---
title: "Creative Aggs Part V : Incremental Aggs"
url: "https://dax.tips/2019/11/11/creative-aggs-part-v-incremental-aggs"
published: "2019-11-10"
updated: "2019-11-14"
source: "dax.tips"
---

I wasn’t originally planning on including an article on Incremental Aggs as part of this series, but I like the idea, and it seems like a good fit for the series, so here goes.

#### The series so far

- [[creative-aggs-part-i-introduction|Introduction to Aggs]]
- [[creative-aggs-part-ii-horizontal-aggs|Horizontal Aggs]]
  - Mixing Agg and base table to produce a single value
- [[creative-aggs-part-iii-accordion-aggs|Accordion Aggs]]
  - Variable grain aggs in a single table for extra flexibility
- [[creative-aggs-part-iv-filtered-aggs|Filtered Aggs]]
  - Using Composite Models to keep recent data in memory, and older data in the source to optimise the model size
- [[creative-aggs-part-v-incremental-aggs|Incremental Aggs]] (this article)
  - A smart way to update Agg tables to optimise refresh time
- [[creative-aggs-part-vi-shadow-models|Shadow Models]]
  - Create manual DUAL mode tables to work around some current limitations of Agg Awareness

### What is Incremental Aggs?

Incremental Aggs provide a mechanism where you can subtly *adjust* the data within the aggregate table – without having to reload the entire table each time the base table changes.

Consider you have a large base table with 100 million rows of data. The base table contains many rows of transaction data per Store, Product and Day. You also have an aggregate table in your model that summarises the base table down to **a single row** per Store, Product and Day. The aggregate version of this table is 10 million rows.

To keep things simple, imagine the base table then has the following three actions take place:

- 1 new row is added to the base table some point back in time
- 1 existing row gets removed from the base table
- 1 existing row has a value changed

The above changes are known as a restatement and mean the base table is now no longer in sync with the aggregate table.

Our options to re-sync the agg table to the base table are to either dump the aggregate table entirely and rebuild from scratch, or apply a small delta to the existing aggregate table.

Dumping and rebuilding the entire agg table seems overkill, especially if the changes made to a large base table are tiny such as this.

Or, we can try the Incremental Agg approach. To bring the agg table back in sync with the base table, three rows **get added** to the agg table. Each row includes relevant key values (Store, Product and Day) and consists of *an adjustment value* to reflect the restatement. These appended rows act like a delta to the existing data.

In the case of the added row, this will be a positive value. In the case of a delete (or update) operation to the base table, this value will be negative or positive.

The result, once the delta rows get added, should be that operations such as SUM should return the same value when using either the base or agg table.

A simplified example gets shown below. The base table on the left shows a table with 12 rows which has been aggregated down to just four rows in the table on the right.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/11/B1.png?resize=786%2C460&ssl=1)

Before restatement – The base table is in sync with Agg table

In the image below, three “restatement” operations have taken place to the base table.

- The 2nd row got deleted.
- The fourth row has a new value in the Quantity column (10 instead of 4)
- The bottom row shows a new transaction not included originally.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/11/B2.png?resize=798%2C502&ssl=1)

Restatement

The agg table on the right now includes an additional three rows of data. These new rows are the delta rows. The agg table will now still produce the same result as the base table when performing a SUM over the Quantity column.

In this super simplified example, the ratio of delta rows in the agg table to the total number of rows means you wouldn’t get many benefit here. However, if you have a much larger base and agg tables, this approach can reduce the amount of refresh time needed to update the agg table.

### When might you use Incremental Aggs

The larger the base and agg tables are, the more beneficial this approach becomes, especially if you have a requirement to refresh your model multiple times per day. Incrementally updating the agg table helps to keep refresh times to a minimum. This technique isn’t just for multi-billion row tables; it can also pay dividends in terms in optimising the CPU/IO loads over your AS instance.

### Things to consider

This methodology requires you to add new logic in the ETL layer to keep track of changes to the base table with an awareness of what gets generated for the agg table. This logic has to reset when the base and agg tables are periodically re-consolidated.

At some point, the additional “delta” rows added to the aggregate table will negate the benefit of the agg table – so a process to consolidate the Agg table needs to take place. This consolidation could happen daily, weekly or monthly, depending on how often restatement occurs to your base table. One way to consolidate your agg table is to truncate the agg table in the source and rebuild from the base table. Once you finish loading the agg table into your model, the agg and base tables should now be in sync.

Power BI Premium includes a handy feature called [Incremental Refresh](https://docs.microsoft.com/en-us/power-bi/service-premium-incremental-refresh) which takes care of much of the scripting required to manage to get the new rows added to the aggregate table. In this case, I would recommend following the steps outlined here under [Detect Data Change](https://docs.microsoft.com/en-us/power-bi/service-premium-incremental-refresh#detect-data-changes) section of the MS doc.

If you don’t have Power BI Premium and can’t take advantage of Incremental Refresh, it is well worth reading the approach so you can still apply the technique in other versions of SSAS (Azure Analysis Services, or SSAS). If you are using SSAS, you can perform a [Process Add](https://www.sqlbi.com/articles/using-process-add-in-tabular-models/) to the agg table to bring in the delta rows.

### Summary

I quite like the approach, which is why I wanted to share the idea. It doesn’t suit all models – but if you are using aggs in a model that needs to refresh often, this can potentially make a significant difference to your server.

As always, I’d love to hear your feedback, especially if you implement some or all of this.

5
5
votes

Article Rating
