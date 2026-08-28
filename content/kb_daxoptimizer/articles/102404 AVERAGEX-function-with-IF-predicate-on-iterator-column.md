---
title: "AVERAGEX function with IF predicate on iterator column"
kb_id: "102404"
url: "https://kb.daxoptimizer.com/d/102404"
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
        'Product'[Unit Price] > 0,
        'Product'[Unit Price],
        'Product'[Net Price]
    )
)
```

### Possible optimization

```dax
VAR _Numerator =
    CALCULATE (
        SUMX (
            SUMMARIZE ( 'Product', 'Product'[Product Name], 'Product'[Unit Price] ),
            'Product'[Unit Price]
        ),
        KEEPFILTERS ( 'Product'[Unit Price] > 0 )
    )
        + CALCULATE (
            SUMX (
                SUMMARIZE ( 'Product', 'Product'[Product Name], 'Product'[Unit Price] ),
                'Product'[Net Price]
            ),
            KEEPFILTERS ( NOT (  'Product'[Unit Price] > 0  ) )
        )
VAR _Denominator =
    COUNTROWS (
        SUMMARIZE ( 'Product', 'Product'[Product Name], 'Product'[Unit Price] )
    )
RETURN
    DIVIDE ( _Numerator, _Denominator )
```
