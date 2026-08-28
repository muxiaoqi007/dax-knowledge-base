---
title: "Filter modifier function not used as filter argument"
kb_id: "100800"
url: "https://kb.daxoptimizer.com/d/100800"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Filter modifier function not used as filter argument

A filter modifier function (filter modifier or filter removal) is not used as a filter argument.

## Remarks

A filter modifier like [[ALLEXCEPT]] is materialized when used as a table argument in other functions like [[FILTER]]. The filter modifier should be used as a filter argument, applying other predicates as individual filter arguments in [[CALCULATE]]. This way, the materialization of functions like [[ALLEXCEPT]] is avoided providing better performance.

## Example

Remove the [[FILTER]] function so that [[ALLEXCEPT]] acts as a filter modifier for [[CALCULATE]] and the filter context adds only a column filter on *Sales[Quantity]* instead of filter with the expanded table *Sales* without *Customer* columns.

### Original code

```dax
CALCULATE (
    SUM ( Sales[Quantity] ),
    FILTER (
        ALLEXCEPT ( Sales, Customer ),
        Sales[Quantity] > 1000
    )
)
```

### Possible optimization

```dax
CALCULATE (
    SUM ( Sales[Quantity] ),
    ALLEXCEPT ( Sales, Customer ),
    Sales[Quantity] > 1000
)
```
