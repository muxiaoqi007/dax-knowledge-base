---
title: "A FILTER function is used instead of modifying the filter context"
kb_id: "102501"
url: "https://kb.daxoptimizer.com/d/102501"
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

**Move the predicate in a CALCULATETABLE filter and remove existing filters.** Use [[CALCULATETABLE]] instead of [[FILTER]] to filter the table moving the condition into a filter argument. Remove [[ALL]], wrap the table reference in [[VALUES]], and add [[REMOVEFILTERS]] to preserve the same semantics.

### Original code

```dax
FILTER (
    ALL ( Sales ),
    Sales[Quantity] > 1
)
```

### Possible optimization

```dax
CALCULATETABLE (
    VALUES ( Sales ),
    Sales[Quantity] > 1,
    REMOVEFILTERS ( Sales )
)
```

## Example 2

**Move the predicate in a CALCULATETABLE filter, remove existing filters and group by the required columns.** Remove the [[FILTER]] and use [[CALCULATETABLE]] to filter the [SUMMARIZE](https://dax.guide/summarize) result for the same columns, moving the condition into a filter argument; wrap the table name in [[VALUES]] and add [[REMOVEFILTERS]] to preserve the same semantics.

### Original code

```dax
FILTER (
    ALL ( Sales[Quantity], Sales[Net Price] ),
    Sales[Quantity] > 1
)
```

### Possible optimization

```dax
CALCULATETABLE (
    SUMMARIZE ( VALUES ( Sales ), Sales[Quantity], Sales[Net Price] ),
    Sales[Quantity] > 1,
    REMOVEFILTERS ( Sales )
)
```

## Example 3

**Move the predicate in a CALCULATETABLE filter and remove existing filters.** Use [[CALCULATETABLE]] instead of [[FILTER]] to filter the table moving the condition into a filter argument. Remove [[ALLNOBLANKROW]], wrap the table reference in [[VALUES]], and add [[REMOVEFILTERS]] to preserve the same semantics.

### Original code

```dax
FILTER (
    ALLNOBLANKROW ( Sales) ,
    Sales[Quantity] > 1
)
```

### Possible optimization

```dax
CALCULATETABLE (
    Sales,
    Sales[Quantity] > 1,
    REMOVEFILTERS ( Sales )
)
```

## Example 4

**Move the predicate in a CALCULATETABLE filter, remove existing filters and group by the required columns.** Remove the [[FILTER]] and use [[CALCULATETABLE]] to filter the [SUMMARIZE](https://dax.guide/summarize) result for the same columns, moving the condition into a filter argument; add [[REMOVEFILTERS]] to preserve the same semantics.

### Original code

```dax
FILTER (
    ALLNOBLANKROW ( Sales[Quantity], Sales[Net Price] ),
    Sales[Quantity] > 1
)
```

### Possible optimization

```dax
CALCULATETABLE (
    SUMMARIZE ( Sales, Sales[Quantity], Sales[Net Price] ),
    Sales[Quantity] > 1,
    REMOVEFILTERS ( Sales )
)
```
