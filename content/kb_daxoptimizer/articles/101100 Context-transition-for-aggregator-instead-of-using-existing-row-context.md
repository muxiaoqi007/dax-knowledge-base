---
title: "Context transition for aggregator instead of using existing row context"
kb_id: "101100"
url: "https://kb.daxoptimizer.com/d/101100"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Context transition for aggregator instead of using existing row context

An aggregator function (like [[SUM]], [[MIN]], [[MAX]], …) is executed within a context transition instead of using a column reference in the existing row context.

## Remarks

The row context of an iterator is transformed into a filter context with the context transition produced by [[CALCULATE]], providing a filter context that has only one row when the iterated table has unique rows. In this condition, the aggregation function only groups a single row, returning the same result that would have been obtained by using a column reference on the existing row context.

From a performance standpoint, the context transition consumes CPU and memory, so removing the need of a context transition and removing the aggregation improves performance. The optimization is possible only when the iterated table has unique rows, otherwise the result could be different.

## Example

Remove [[CALCULATE]] and [[SUM]] so that the column reference is evaluated within the row context produced by the iteration over Store.

### Original code

```dax
SUMX ( 
    Store, 
    CALCULATE ( SUM ( Store[Square Meters] ) ) 
)
```

### Possible optimization

```dax
SUMX ( 
    Store, 
    Store[Square Meters] 
)
```
