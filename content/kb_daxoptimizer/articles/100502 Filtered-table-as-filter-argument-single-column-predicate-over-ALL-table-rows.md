---
title: "Filtered table as filter argument (single column predicate over ALL table rows)"
kb_id: "100502"
url: "https://kb.daxoptimizer.com/d/100502"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Filtered table as filter argument (single column predicate over ALL table rows)

A [[FILTER]] function filtering [[ALL]] the rows of an entire table with a single column predicate is used as a filter argument in functions such as [[CALCULATE]], [[CALCULATETABLE]], etc.

## Remarks

The single column predicate can be applied as a filter argument without filtering an entire table.

In order to **keep the same semantics as the original filter**, apply [[REMOVEFILTERS]] on the previously iterated table because the iterator was over [[ALL]] of that table.

Recommended articles:

- [Filter arguments in CALCULATE](https://www.sqlbi.com/articles/filter-arguments-in-calculate/)

## Example

Remove the *Date* iterator and add [[REMOVEFILTERS]] on *Date* so that the filter context is removed from all the other columns of the *Date* table to keep the original semantics once the condition only filters *Date[Year]*.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    FILTER ( 
        ALL ( 'Date' ),
        'Date'[Year] = 2023
    )
)
```

### Possible optimization

```dax
CALCULATE (
    [Sales Amount],
    REMOVEFILTERS ( 'Date' ),
    'Date'[Year] = 2023
)
```
