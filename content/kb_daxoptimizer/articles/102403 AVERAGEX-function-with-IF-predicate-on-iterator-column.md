---
title: "AVERAGEX function with IF predicate on iterator column"
kb_id: "102403"
url: "https://kb.daxoptimizer.com/d/102403"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# AVERAGEX function with IF predicate on iterator column

[[AVERAGEX]] iterator function contains an [[IF]] predicate on a single column filtering the iterated table.

## Remarks

The [[IF]] condition in an iterator can be expensive from a performance standpoint. When the condition only references columns of the iterated table (or the expanded table using [[RELATED]]), then it is possible to filter the table through the filter context by using [[CALCULATE]].

## Example

Remove the [[IF]] condition and use [[CALCULATE]] to filter the table moving the condition into a filter argument; use KEEPFILTERS to preserve the same semantics.

### Original code

```dax
AVERAGEX (
    SUMMARIZE ( 'Product', 'Product'[Product Name], 'Product'[Unit Price] ),
    IF (
        'Product'[Unit Price] > 100,
        'Product'[Unit Price]
    )
)
```

### Possible optimization

```dax
CALCULATE (
    AVERAGEX (
        SUMMARIZE ( 'Product', 'Product'[Product Name], 'Product'[Unit Price] ),
        'Product'[Unit Price]
    ),
    KEEPFILTERS ( 'Product'[Unit Price] > 100 )
)
```
