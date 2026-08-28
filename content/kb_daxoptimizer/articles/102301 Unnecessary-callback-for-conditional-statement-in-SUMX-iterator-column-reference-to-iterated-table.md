---
title: "Unnecessary callback for conditional statement in SUMX iterator (column reference to iterated table)"
kb_id: "102301"
url: "https://kb.daxoptimizer.com/d/102301"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Unnecessary callback for conditional statement in SUMX iterator (column reference to iterated table)

The [[SUMX]] iterator uses a conditional [[IF]] statement expression that can be rewritten removing unnecessary callbacks to the formula engine. The conditional statement references one or more columns that are part of the iterated table and correspond to columns of the data model.

## Remarks

The [[IF]] function inside an iterator can produce a callback to the formula engine that affects performance. When the iterator is [[SUMX]], it is possible to arrange the code in different ways thanks to the additivity of the result.

When the [[IF]] condition depends on a column of the iterated table, it should be possible to split the calculation in two different [[CALCULATE]] functions that are summed together. Each [[CALCULATE]] evaluates one of the two branches of the [[IF]] function and their result is summed to return the same value of the original expression.

Read more about this optimization:

- [Optimizing mutually exclusive calculations](https://www.sqlbi.com/articles/optimizing-mutually-exclusive-calculations/)

## Example

Duplicate the original [[SUMX]] function, filter the first one with the original [[IF]] condition and the second one with the same condition embedded in a [NOT](https://dax.guide/op/not/)) operator.

### Original code

```dax
SUMX ( 
    Sales,
    IF ( 
        Sales[Quantity] > 1,
        Sales[Quantity] * Sales[Net Price], 
        Sales[Quantity] * Sales[Unit Price] 
    )
)
```

### Possible optimization

```dax
CALCULATE (
    SUMX ( Sales, Sales[Quantity] * Sales[Net Price] ),
    Sales[Quantity] > 1
)
+
CALCULATE (
    SUMX ( Sales, Sales[Quantity] * Sales[Unit Price] ),
    NOT ( Sales[Quantity] > 1 )
)
```
