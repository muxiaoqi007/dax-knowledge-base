---
title: "A FILTER function is used instead of modifying the filter context"
kb_id: "102505"
url: "https://kb.daxoptimizer.com/d/102505"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# A FILTER function is used instead of modifying the filter context

The [[FILTER]] function is used instead of modifying the filter context.

## Remarks

The [[FILTER]] function can be expensive from a materialization standpoint. When the filter condition only references physical columns of the iterated table, then it is possible to filter the table through the filter context by using [[KEEPFILTERS]].

The pattern [[FILTER]]/[[VALUES]] over a column to add a filter to the existing filters in a [[CALCULATE]] function is less efficient than using [[KEEPFILTERS]] over the predicate because the evaluation of the filter is potentially different for every cell in a report. A simple predicate requires a single evaluation of the filter, which is applied without removing the existing filters when it is embedded in [[KEEPFILTERS]] to obtain the same semantic, usually resulting in better performance.

## Example

**Transform FILTER in KEEPFILTERS.** Replace [[FILTER]]/[[VALUES]] with [[KEEPFILTERS]] over the predicate to modify the filter context while preserving the same semantics.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    FILTER (
        VALUES ( Sales[Order Number] ),
        Sales[Quantity] > 1
    )
)
```

### Possible optimization

```dax
CALCULATE (
    [Sales Amount],
    KEEPFILTERS ( Sales[Quantity] > 1 )
)
```
