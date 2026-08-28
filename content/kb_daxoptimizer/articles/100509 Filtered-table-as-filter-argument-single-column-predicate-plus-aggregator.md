---
title: "Filtered table as filter argument (single column predicate plus aggregator)"
kb_id: "100509"
url: "https://kb.daxoptimizer.com/d/100509"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Filtered table as filter argument (single column predicate plus aggregator)

A [[FILTER]] function filtering an entire table with a single column predicate and an aggregator is used as a filter argument in functions such as [[CALCULATE]], [[CALCULATETABLE]], etc.

## Remarks

The single column predicate can be applied as a filter argument without filtering an entire table. The aggregator should be moved into a variable assigned before [[CALCULATE]] or [[CALCULATETABLE]].

By removing the table, it is important to **keep the same semantics as the original filter**, so that the result is not affected. To do that, apply [[REMOVEFILTERS]] or [[KEEPFILTERS]] as needed.

Recommended articles:

- [Filter arguments in CALCULATE](https://www.sqlbi.com/articles/filter-arguments-in-calculate/)
- [Using KEEPFILTERS](https://www.sqlbi.com/articles/using-keepfilters-in-dax-updated/)

## Example 1

Move the [[MAX]] aggregation outside [[CALCULATE]] and store the result in a variable. Remove the *Date* iterator and add [[REMOVEFILTERS]] on *Date* so that the filter context is removed from all the other columns of the *Date* table to keep the original semantics once the condition only filters *Date[Year]*.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    FILTER (
        ALL ( 'Date' ),
        'Date'[Date] <= MAX ( 'Date'[Date] )
    )
)
```

### Possible optimization

```dax
VAR _MaxDate = MAX ( 'Date'[Date] )
RETURN
    CALCULATE (
        [Sales Amount],
        REMOVEFILTERS ( 'Date' ),
        'Date'[Date] <= _MaxDate
    )
```

## Example 2

Move the [[MAX]] aggregation outside [[CALCULATE]] and store the result in a variable. Remove the *Date* iterator and add [[REMOVEFILTERS|KEEPFILTERS]] around the filter argument to maintain the same semantics, so the filter is applied only to the *Date[Date]* column instead of filtering the entire *Date* table.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    FILTER (
        'Date',
        'Date'[Date] = MAX ( 'Date'[Date] )
    )
)
```

### Possible optimization

```dax
VAR _MaxDate = MAX ( 'Date'[Date] )
RETURN
    CALCULATE (
        [Sales Amount],
        KEEPFILTERS ( 'Date'[Date] = _MaxDate )
    )
```
