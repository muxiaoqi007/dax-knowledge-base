---
title: "Filtered table as filter argument (multiple columns predicate with OR condition)"
kb_id: "100505"
url: "https://kb.daxoptimizer.com/d/100505"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Filtered table as filter argument (multiple columns predicate with OR condition)

A [[FILTER]] function filtering entire table using an [OR](https://dax.guide/op/or/) condition on multiple columns is used as a filter argument in functions such as [[CALCULATE]], [[CALCULATETABLE]], etc.

## Remarks

Because the conditions are applied with an [||](https://dax.guide/op/or/) operator, the multiple columns predicate must be kept in a single filter argument, without filtering an entire table.

In order to **keep the same semantics as the original filter**, apply [[KEEPFILTERS]] over the filter argument.

Recommended articles:

- [Filter arguments in CALCULATE](https://www.sqlbi.com/articles/filter-arguments-in-calculate/)
- [Using KEEPFILTERS](https://www.sqlbi.com/articles/using-keepfilters-in-dax-updated/)

## Example

Remove the *Customer* iterator and use [[KEEPFILTERS]] around the predicate to maintain the same semantics, so the filter is applied only to the *Customer[Country]* and *Customer[Age]* columns instead of filtering the entire *Customer* table.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    FILTER (
        Customer,
        Customer[Country] = "Canada" 
            || Customer[Age] >= 21
    )
)
```

### Possible optimization

```dax
CALCULATE (
    [Sales Amount],
    KEEPFILTERS ( 
        Customer[Country] = "Canada" 
            ||  Customer[Age] >= 21 
    )
)
```
