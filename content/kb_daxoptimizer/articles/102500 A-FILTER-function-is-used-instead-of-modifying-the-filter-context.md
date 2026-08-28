---
title: "A FILTER function is used instead of modifying the filter context"
kb_id: "102500"
url: "https://kb.daxoptimizer.com/d/102500"
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

**Move the predicate in a CALCULATETABLE filter.** Remove the [[FILTER]] and use [[CALCULATETABLE]] to filter the table moving the condition into a filter argument; use [[KEEPFILTERS]] to preserve the same semantics.

### Original code

```dax
FILTER (
    SUMMARIZE (
        Sales,
        Sales[Quantity],
        Sales[Net Price]
    ),
    Sales[Quantity] > 1
)
```

### Possible optimization

```dax
CALCULATETABLE (
    SUMMARIZE (
        Sales,
        Sales[Quantity],
        Sales[Net Price]
    ),
    KEEPFILTERS ( Sales[Quantity] > 1 )
)
```

## Example 2

**Move the predicate in a CALCULATETABLE filter.** Remove the [[FILTER]] and use [[CALCULATETABLE]] to filter the table moving the condition into a filter argument; use [[KEEPFILTERS]] to preserve the same semantics.

### Original code

```dax
FILTER (
    ADDCOLUMNS (
        SUMMARIZE (
            Sales,
            Sales[Quantity],
            Sales[Net Price]
        ),
        "@Discount", Sales[Net Price] * 0.2
    ),
    Sales[Quantity] > 1
)
```

### Possible optimization

```dax
CALCULATETABLE (
    ADDCOLUMNS (
        SUMMARIZE (
            Sales,
            Sales[Quantity],
            Sales[Net Price]
        ),
        "@Discount", Sales[Net Price] * 0.2
    ),
    KEEPFILTERS ( Sales[Quantity] > 1 )
)
```
