---
title: "Filtered table as filter argument (single column predicate)"
kb_id: "100501"
url: "https://kb.daxoptimizer.com/d/100501"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Filtered table as filter argument (single column predicate)

A [[FILTER]] function filtering an entire table with a single column predicate is used as a filter argument in functions such as [[CALCULATE]], [[CALCULATETABLE]], etc.

## Remarks

The single column predicate can be applied as a filter argument without filtering an entire table.

In order to **keep the same semantics as the original filter**, apply [[KEEPFILTERS]] over the filter argument.

Recommended articles:

- [Filter arguments in CALCULATE](https://www.sqlbi.com/articles/filter-arguments-in-calculate/)
- [Using KEEPFILTERS](https://www.sqlbi.com/articles/using-keepfilters-in-dax-updated/)

## Example

Remove the *Sales* iterator and use [[KEEPFILTERS]] around the condition to maintain the same semantics, so the filter is applied only to the *Sales[Unit Price]* column specified in the predicate instead of using the entire *Sales* table.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    FILTER ( Sales, Sales[Unit Price] > 100 )
)
```

### Possible optimization

```dax
CALCULATE (
    [Sales Amount],
    KEEPFILTERS ( Sales[Unit Price] > 100 )
)
```
