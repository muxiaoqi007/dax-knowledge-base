---
title: "Unnecessary callback for conditional statement in SUMX iterator (no column references)"
kb_id: "102300"
url: "https://kb.daxoptimizer.com/d/102300"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Unnecessary callback for conditional statement in SUMX iterator (no column references)

The [[SUMX]] iterator uses a conditional [[IF]] statement expression that can be rewritten removing unnecessary callbacks to the formula engine. The conditional statement does not reference columns that are part of the data model.

## Remarks

The [[IF]] function inside an iterator can produce a callback to the formula engine that affects performance. When the iterator is [[SUMX]], it is possible to arrange the code in different ways thanks to the additivity of the result. When the [[IF]] condition does not depend on the iterated table, it should be possible to move the condition outside of the iterator, reducing or removing the callback overhead.

## Example

Assign the result of the [[IF]] function in a variable before the [[SUMX]] function and use the variable in the iterated expression.

### Original code

```dax
SUMX ( 
    Sales,
    Sales[Quantity] * Sales[Net Price] 
        * IF ( MONTH ( TODAY () ) > 6, 1.1, 1 )
)
```

### Possible optimization

```dax
VAR _Parameter =
    IF ( MONTH ( TODAY () ) > 6, 1.1, 1 )
RETURN
    SUMX ( 
        Sales,
        Sales[Quantity] * Sales[Net Price] 
            * _Parameter
    )
```
