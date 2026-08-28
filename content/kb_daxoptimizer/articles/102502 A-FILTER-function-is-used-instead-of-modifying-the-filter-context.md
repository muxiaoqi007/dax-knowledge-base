---
title: "A FILTER function is used instead of modifying the filter context"
kb_id: "102502"
url: "https://kb.daxoptimizer.com/d/102502"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# A FILTER function is used instead of modifying the filter context

The [[FILTER]] function is used instead of modifying the filter context.

## Remarks

The [[FILTER]] function can be expensive from a materialization standpoint. When the filter condition only references physical columns of the iterated table, then it is possible to filter the table through the filter context by using [[CALCULATETABLE]].

## Example

**Move the predicate in a CALCULATETABLE filter using ALLSELECTED as a filter modifier.** Use [[CALCULATETABLE]] instead of [[FILTER]] to filter the table moving the condition into a filter argument. Remove [[ALLSELECTED]] from the first argument and apply it as a filted modifier to preserve the same semantics.

### Original code

```dax
FILTER (
    ALLSELECTED ( Sales ),
    Sales[Order Date] <= MAX( Sales[Order Date] )
)
```

### Possible optimization

```dax
CALCULATETABLE (
    Sales,
    Sales[Order Date] <= MAX( Sales[Order Date] ),
    ALLSELECTED ( Sales )
)
```
