---
title: "A FILTER function is used instead of modifying the filter context"
kb_id: "102503"
url: "https://kb.daxoptimizer.com/d/102503"
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

**Move the predicate in a CALCULATETABLE filter using ALLEXCEPT as a filter modifier.** If the result of [[ALLEXCEPT]] is filter in [[CALCULATE]] or [[CALCULATETABLE]], then [[ALLEXCEPT]] should be used as a filter argument moving the condition into a filter argument. If the result of [[ALLEXCEPT]] is used as a table for other purposes and not as a filter in [[CALCULATE]] or [[CALCULATETABLE]], then the code cannot be optimized.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    FILTER (
        ALLEXCEPT ( Sales, Sales[Order Number] ),
        Sales[Quantity] > 1    
    )
)
```

### Possible optimization

```dax
CALCULATE (
    [Sales Amount],
    ALLEXCEPT ( Sales, Sales[Order Number] ),
    Sales[Quantity] > 1
)
```
