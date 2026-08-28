---
title: "A FILTER function is used instead of modifying the filter context"
kb_id: "102504"
url: "https://kb.daxoptimizer.com/d/102504"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# A FILTER function is used instead of modifying the filter context

The [[FILTER]] function is used instead of modifying the filter context.

## Remarks

The [[FILTER]] function can be expensive from a materialization standpoint. When the filter condition only references physical columns of the iterated table, then it is possible to filter the table through the filter context by using [[CALCULATETABLE]].

## Example 1

**Transform FILTER/RELATEDTABLE in CALCULATETABLE.** Remove [[FILTER]] and [RELATEDTABLE](https://dax.guide/calculatetable), replacing them with [[CALCULATETABLE]]. Use [[KEEPFILTERS]] over the predicate to preserve the same semantics.

### Original code

```dax
FILTER (
    RELATEDTABLE ( Sales ),
    Sales[Quantity] > 1
)
```

### Possible optimization

```dax
CALCULATETABLE (
    Sales,
    KEEPFILTERS ( Sales[Quantity] > 1 )
)
```
