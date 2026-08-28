---
title: "Filtered table as filter argument (multiple columns predicate with AND condition)"
kb_id: "100503"
url: "https://kb.daxoptimizer.com/d/100503"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Filtered table as filter argument (multiple columns predicate with AND condition)

A [[FILTER]] function filtering entire table using an [AND](https://dax.guide/op/and/) condition on multiple columns is used as a filter argument in functions such as [[CALCULATE]], [[CALCULATETABLE]], etc.

## Remarks

Because the conditions are applied with an [&&](https://dax.guide/op/and/) operator, the multiple columns predicate can be transformed into one filter argument for each column, without filtering an entire table.

In order to **keep the same semantics as the original filter**, apply [[KEEPFILTERS]] over each filter argument.

Recommended articles:

- [Filter arguments in CALCULATE](https://www.sqlbi.com/articles/filter-arguments-in-calculate/)
- [Using KEEPFILTERS](https://www.sqlbi.com/articles/using-keepfilters-in-dax-updated/)

## Example

Remove the *Customer* iterator and use [[KEEPFILTERS]] around each condition to maintain the same semantics, so the filter is applied only to the *Customer[Country]* and *Customer[Age]* columns instead of filtering the entire *Customer* table.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    FILTER ( 
        Customer,
        Customer[Country] = "Canada" 
            && Customer[Age] >= 21
    )
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
