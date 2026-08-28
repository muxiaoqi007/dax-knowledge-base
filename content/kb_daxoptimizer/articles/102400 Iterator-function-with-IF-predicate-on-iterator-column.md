---
title: "Iterator function with IF predicate on iterator column"
kb_id: "102400"
url: "https://kb.daxoptimizer.com/d/102400"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Iterator function with IF predicate on iterator column

The iterator function contains an [[IF]] predicate on a single column filtering the iterated table.

## Remarks

The [[IF]] condition in an iterator can be expensive from a performance standpoint. When the condition only references columns of the iterated table (or the expanded table using [[RELATED]]), then it is possible to filter the table through the filter context by using [[CALCULATE]].

## Example

Remove the [[IF]] condition and use [[CALCULATE]] to filter the table moving the condition into a filter argument; use KEEPFILTERS to preserve the same semantics.

### Original code

```dax
SUMX (
    Sales,
    IF ( Sales[Quantity] > 0, Sales[Quantity] * Sales[Net Price] )
)</span>
```

### Possible optimization

```dax
CALCULATE ( 
    SUMX (
        Sales,
        Sales[Quantity] * Sales[Net Price]
    ),
    KEEPFILTERS ( Sales[Quantity] > 0 )
)</span>
```
