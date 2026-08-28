---
title: "PRODUCTX function with IF predicate on iterator column"
kb_id: "102402"
url: "https://kb.daxoptimizer.com/d/102402"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# PRODUCTX function with IF predicate on iterator column

[[PRODUCTX]] iterator function contains an [[IF]] predicate on a single column filtering the iterated table.

## Remarks

The [[IF]] condition in an iterator can be expensive from a performance standpoint. When the condition only references columns of the iterated table (or the expanded table using [[RELATED]]), then it is possible to filter the table through the filter context by using [[CALCULATE]].

## Example

Remove the [[IF]] condition and use [[CALCULATE]] to filter the table moving the condition into a filter argument; use KEEPFILTERS to preserve the same semantics.

### Original code

```dax
PRODUCTX (
    Rates,
    IF (
        Rates[Rate] > 0,
        Rates[Rate]
    )
)
```

### Possible optimization

```dax
CALCULATE (
    PRODUCTX (
        Rates,
        Rates[Rate]
    ),
    KEEPFILTERS ( Rates[Rate] > 0 )
)
```
