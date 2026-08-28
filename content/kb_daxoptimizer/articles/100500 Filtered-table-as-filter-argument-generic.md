---
title: "Filtered table as filter argument (generic)"
kb_id: "100500"
url: "https://kb.daxoptimizer.com/d/100500"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Filtered table as filter argument (generic)

A [[FILTER]] function filtering an entire table is used as a filter argument in functions such as [[CALCULATE]], [[CALCULATETABLE]], etc.

## Remarks

Instead of materializing an entire table in the filter context, it is better to only filter the columns involved.

By removing the table, it is important to **keep the same semantics as the original filter**, so that the result is not affected. To do that, apply [[REMOVEFILTERS]] or [[KEEPFILTERS]] as needed.

Recommended articles:

- [Filter arguments in CALCULATE](https://www.sqlbi.com/articles/filter-arguments-in-calculate/)
- [Using KEEPFILTERS](https://www.sqlbi.com/articles/using-keepfilters-in-dax-updated/)

## Example 1

Move the [[AVERAGE]] aggregation before [[CALCULATE]] and store the result in a variable. Remove the *Sales* iterator and use [[KEEPFILTERS]] around the condition to maintain the same semantics, so the filter is applied only to the *Sales[Unit Price]* column specified in the predicate instead of using the entire *Sales* table.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    FILTER ( Sales, Sales[Unit Price] > AVERAGE ( Sales[Unit Price] ) )
)
```

### Possible optimization

```dax
VAR _AveragePrice = AVERAGE ( Sales[Unit Price] )
RETURN
    CALCULATE (
        [Sales Amount],
        KEEPFILTERS ( Sales[Unit Price] > _AveragePrice )
    )
```

## Example 2

Move the [[MAX]] aggregation before [[CALCULATE]] and store the result in a variable. Remove the *Date* iterator and add [[REMOVEFILTERS]] on *Date* so that the filter context is removed from all the other columns of the *Date* table to keep the original semantics once the condition only filters *Date[Year]*.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    FILTER ( 
        ALL ( 'Date' ),
        'Date'[Year] = YEAR ( MAX ( 'Date'[Date] ) )
    )
)
```

### Possible optimization

```dax
VAR _Year = YEAR ( MAX ( 'Date'[Date] ) )
RETURN
RETURN
    CALCULATE (
        [Sales Amount],
        REMOVEFILTERS ( 'Date' ),
        'Date'[Year] = _Year
    )
```
