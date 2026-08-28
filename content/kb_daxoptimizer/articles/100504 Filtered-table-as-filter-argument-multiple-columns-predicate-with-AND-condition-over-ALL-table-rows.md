---
title: "Filtered table as filter argument (multiple columns predicate with AND condition over ALL table rows)"
kb_id: "100504"
url: "https://kb.daxoptimizer.com/d/100504"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Filtered table as filter argument (multiple columns predicate with AND condition over ALL table rows)

A [[FILTER]] function filtering [[ALL]] the rows of an entire table using an [AND](https://dax.guide/op/and/) condition on multiple columns is used as a filter argument in functions such as [[CALCULATE]], [[CALCULATETABLE]], etc.

## Remarks

Because the conditions are applied with an [&&](https://dax.guide/op/and/) operator, the multiple columns predicate can be transformed into one filter argument for each column, without filtering an entire table.

In order to **keep the same semantics as the original filter**, apply [[REMOVEFILTERS]] on the previously iterated table because the iterator was over [[ALL]] of that table.

Recommended articles:

- [Filter arguments in CALCULATE](https://www.sqlbi.com/articles/filter-arguments-in-calculate/)

## Example

Replace the *Customer* iterator with a [[REMOVEFILTERS]] over *Customer*, and move each [&&](https://dax.guide/op/and/) condition in a different filter argument, so the filter is applied only to the *Customer[Country]* and *Customer[Age]* columns instead of filtering the entire *Customer* table.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    FILTER (
        ALL ( Customer ),
        Customer[Country] = "Canada" 
            && Customer[Age] >= 21
    )
)
```

### Possible optimization

```dax
CALCULATE (
    [Sales Amount],
    REMOVEFILTERS ( Customer ),
    Customer[Country] = "Canada",
    Customer[Age] >= 21
)
```
