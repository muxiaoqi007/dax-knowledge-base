---
title: "Redundant functions HASONEVALUE/SELECTEDVALUE, duplicated expression in arg. 3 of both IF functions"
kb_id: "101501"
url: "https://kb.daxoptimizer.com/d/101501"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Redundant functions HASONEVALUE/SELECTEDVALUE, duplicated expression in arg. 3 of both IF functions

The expression involving [[HASONEVALUE]] and [[SELECTEDVALUE]] functions can be optimized by removing [[HASONEVALUE]], reducing the redundant expression in the third argument of both [[IF]] functions.

## Remarks

[[SELECTEDVALUE]] returns a non-blank value when there is only one value visible in the referenced column. Embedding [[SELECTEDVALUE]] in a condition related to [[HASONEVALUE]] seems redundant: removing [[HASONEVALUE]] can slightly improve the performance.

The third argument of both [[IF]] functions is duplicated, which allows to rely on the inner [[IF]] functions also for the cases covered by the outer [[IF]] function.

## Example

Remove the outer [[IF]] function, the [[HASONEVALUE]] condition, and the third argument of the outer [[IF]] function, making sure that it is the same expression as the third argument of the inner [[IF]] function.

### Original code

```dax
IF (
    HASONEVALUE( Customer[Country] ),
    IF (
        SELECTEDVALUE( Customer[Country] ) = "Canada",
        BLANK(),
        [Sales Amount]
    ),
    [Sales Amount]
)
```

### Possible optimization

```dax
IF (
    SELECTEDVALUE( Customer[Country] ) = "Canada",
    BLANK(),
    [Sales Amount]
)
```
