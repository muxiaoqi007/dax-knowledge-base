---
title: "Context transition in iterator without a unique key"
kb_id: "102600"
url: "https://kb.daxoptimizer.com/d/102600"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Context transition in iterator without a unique key

A [context transition](https://www.sqlbi.com/articles/understanding-context-transition-in-dax/) is executed within an iterator over a table that is not guaranteed to have unique rows because there are no unique columns.

## Remarks

When a context transition occurs and the row context is generated from a physical table without at least one unique key, the measure may produce wrong results in the case of duplicate rows.

A unique key exists when at least one column is on the “one” side of a one-to-many relationship, or when a column has been explicitly marked with IsKey = true in the Tabular model, which can be done in Power BI by setting the Key column property in a table.

This recommendation is intended as a best practice, highlighting a potential issue rather than indicating an existing one. When this recommendation is a false positive, it is advisable to either modify the model and set a key column for the table, or rewrite the code to explicitly clarify the intention. For example, consider using SUMMARIZE to identify the combination of columns that defines the uniqueness of the row instead of iterating over the physical table to return accurate results and define an explicit dependency of the calculation from the identified columns.

## Example

**Replace the table reference with a SUMMARIZE function that identifies the unique key columns.** If the row in *Sales* is uniquely identified by *Order Number* and *Order Line Number*, iterate the result of [SUMMARIZE](https://dax.guide/summarize) that groups *Sales* by those two columns. The materialization created by [SUMMARIZE](https://dax.guide/summarize) can be expensive when two or more columns are involved, but it guarantees the correctness of the calculation.

### Original code

```dax
SUMX (
    Sales, 
    [Sales Amount]
)
```

### Possible optimization

```dax
SUMX (
    SUMMARIZE (
        Sales, 
        Sales[Order Number], 
        Sales[Order Line Number]
    ), 
    [Sales Amount]
)
```
