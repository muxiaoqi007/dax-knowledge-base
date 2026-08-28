---
title: "AND condition between different columns in the same filter argument"
kb_id: "102700"
url: "https://kb.daxoptimizer.com/d/102700"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# AND condition between different columns in the same filter argument

When a scalar predicate filter argument contains one or more `&&` conditions between different columns, performance can be improved by using multiple filter arguments, each referencing a single column and a separate condition.

## Remarks

A filter made by multiple columns of the same table may require a table materialization produced by the unique combinations of those columns.

When the predicates are in logical AND conditions, the same effect can be obtained more efficiently by using separate column filters, one for each column. If [[KEEPFILTERS]] is used, it must be applied to all the separate conditions.

Recommended articles:

- [Specifying multiple filter conditions in CALCULATE](https://www.sqlbi.com/articles/specifying-multiple-filter-conditions-in-calculate/)

## Example 1

**Split the predicates in logical AND conditions in independent filters.** Every filter argument should include all the predicates related to a single column.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    Customer[Country] = "Canada" && Customer[Age] >= 21
)
```

### Possible optimization

```dax
CALCULATE (
    [Sales Amount],
    Customer[Country] = "Canada",
    Customer[Age] >= 21
)
```

## Example 2

**Split the predicates in logical AND conditions in independent filters.** Every filter argument should include all the predicates related to a single column. If present, [[KEEPFILTERS]] must be applied to each filter obtained by splitting the original condition.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    KEEPFILTERS ( Customer[Country] = "Canada" && Customer[Age] >= 21 )
)
```

### Possible optimization

```dax
CALCULATE (
    [Sales Amount],
    KEEPFILTERS ( Customer[Country] = "Canada" ),
    KEEPFILTERS ( Customer[Age] >= 21 )
)
```

## Example 3

**Split the predicates in logical AND conditions in independent filters.** Every filter argument should include all the predicates related to a single column. If there are multiple conditions on the same column, they should be part of the same filter argument, like *Customer[Age]*.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    Customer[Country] = "Canada" && Customer[Age] >= 21 && Customer[Age] <= 30
)
```

### Possible optimization

```dax
CALCULATE (
    [Sales Amount],
    Customer[Country] = "Canada",
    Customer[Age] >= 21 && Customer[Age] <= 30
)
```
