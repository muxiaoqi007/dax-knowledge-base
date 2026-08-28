---
title: "Filtered table as filter argument (multiple columns predicate with AND and OR conditions)"
kb_id: "100507"
url: "https://kb.daxoptimizer.com/d/100507"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Filtered table as filter argument (multiple columns predicate with AND and OR conditions)

A [[FILTER]] function filtering entire table using [AND](https://dax.guide/op/and/) and [OR](https://dax.guide/op/or/) conditions on multiple columns is used as a filter argument in functions such as [[CALCULATE]], [[CALCULATETABLE]], etc.

## Remarks

Because the conditions are applied with an [||](https://dax.guide/op/or/) operator, the multiple columns predicate must be kept in a single filter argument, without filtering an entire table.

In order to **keep the same semantics as the original filter**, apply [[REMOVEFILTERS]] on the previously iterated table because the iterator was over [[ALL]] of that table.

Recommended articles:

- [Filter arguments in CALCULATE](https://www.sqlbi.com/articles/filter-arguments-in-calculate/)

## Example

Remove the *Customer* iterator and use [[KEEPFILTERS]] around the predicates to maintain the same semantics, so the filter is applied only to the *Customer[Continent]*, *Customer[City]*, and *Customer[Age]* columns instead of filtering the entire *Customer* table.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    FILTER (
        Customer,
        Customer[Continent] = "North America" 
            && ( Customer[Age] >= 21 || Customer[City] = "Brighton" )
    )
)
```

### Possible optimization

```dax
CALCULATE (
    [Sales Amount],
    KEEPFILTERS ( Customer[Continent] = "North America" ),
    KEEPFILTERS ( Customer[Age] >= 21 || Customer[City] = "Brighton" )
)
```
