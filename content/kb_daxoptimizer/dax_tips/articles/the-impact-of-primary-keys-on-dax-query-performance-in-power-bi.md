---
title: "The Impact of Primary Keys on DAX Query Performance in Power BI"
url: "https://dax.tips/2025/11/14/the-impact-of-primary-keys-on-dax-query-performance-in-power-bi"
published: "2025-11-14"
updated: "2025-11-14"
source: "dax.tips"
---

![](https://i0.wp.com/dax.tips/wp-content/uploads/2025/11/A-sleek-modern-data-visualisation-concept-illustr.png?resize=1000%2C1000&ssl=1)

When writing DAX queries, performance tuning often comes down to small design decisions that have big consequences. One such decision is whether to include **Primary Key columns from Dimension tables** in your `SUMMARIZECOLUMNS` statements. This is particularly important when those Dimension tables use **DUAL** or **IMPORT** storage modes.

This article explains why doing so can lead to inefficient query plans. It describes what happens under the hood. It also shows how to avoid this common pitfall.

## The Problem: From SPARSE to DENSE Queries

Including a Primary Key as a group-by column in a `SUMMARIZECOLUMNS` statement changes the query behaviour. Instead of operating as a **SPARSE query**, which processes only combinations that exist in the data, the query becomes **DENSE**.

## What Does This Mean?

- **SPARSE Query**: Efficiently aggregates only the rows that exist in the fact table.
- **DENSE Query**: The engine iterates through **every possible combination** of grouped keys. It does this even for combinations that don’t exist in the fact table.

This shift introduces an **outer join step** in the query plan, which:

- Iterates through all combinations of the Primary Key and other grouping columns.
- For each combination, checks against the output of a separate storage engine event.
- Discards the vast majority of iterations because they return no data.

## Visualising the impact

![](https://i0.wp.com/dax.tips/wp-content/uploads/2025/11/image-2.png?resize=1000%2C202&ssl=1)

## Why Does This Hurt Performance?

- **Extra Storage Engine Events**: Each iteration triggers extra, often unfiltered scans.
- **Increased CPU and Memory Usage**: The engine wastes resources on empty combinations.
- **Slower Queries**: Instead of combining steps into one efficient operation, the engine performs redundant checks.

In large models, this can lead to **significant slowdowns** and poor scalability.

## Example: Bad vs Good

### Bad Approach

Including the Primary Key:

```dax
EVALUATE
SUMMARIZECOLUMNS (
    DimCustomer[CustomerKey],  // Primary Key from Dimension
    DimCustomer[Country],
    "Sales", SUM ( Sales[Amount] )
)

```

### Better Approach

Exclude the Primary Key:

```dax
EVALUATE
SUMMARIZECOLUMNS (
    DimCustomer[Country],
    "Sales", SUM ( Sales[Amount] )
)

```

By removing the Primary Key, the query avoids unnecessary outer joins. It also prevents redundant iterations. This results in a leaner, faster execution plan.

## Recent example

I helped on a recent example where a reasonably clean set of Server Timings was shared. The query took 38 seconds despite spending only a tiny part of that time collecting data. The presence of the scan at line 8 shows a “Dimension Scan” that has no aggregate task (SUM, MAX, AVG etc.). The output of this event is an intermediary table with 831,278 rows to be iterated over. The large yellow section in the Timeline chart suggests the amount of time spent on the DENSE loop.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2025/11/image-3.png?resize=1000%2C156&ssl=1)

## Best Practices

**Do not include Primary Key columns from Dimension tables in SUMMARIZECOLUMNS** unless absolutely necessary. Group by **attributes relevant to your calculation**, not identifiers. Let the engine optimise for **SPARSE queries**, which only process combinations where data exists.

Create a cloned version of the column in the dimension table if need. This new column can carry the value from the PK column. Use this column in your grouping set in place of the PK column.

Hide **ALL** columns in your semantic model that get used either side of any relationship.

## Summary

A Primary Key column gets used on the axis of a Power BI Visual. These columns are included as group-by columns in `SUMMARIZECOLUMNS`. This inclusion can turn an efficient SPARSE query into a costly DENSE query. This results in extra storage engine events, wasted resources, and slower performance. Avoid this by grouping only on meaningful attributes.

5
1
vote

Article Rating
