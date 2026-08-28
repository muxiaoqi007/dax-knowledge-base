---
title: "Blank comparison"
kb_id: "100900"
url: "https://kb.daxoptimizer.com/d/100900"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Blank comparison

[[BLANK]] is used for comparison in a binary operator where the operator is not strict equal to (==).

## Remarks

When the [[BLANK]] function is used as an operand in a binary comparison operation, the engine performs a transformation to compare the computed value with both 0 and [[BLANK]], which has a small CPU cost.
Moreover, a comparison with BLANK is often incorrect as 0 or an empty string (“”) returns TRUE if compared with [=](https://dax.guide/op/equal-to/) to [[BLANK]].

## Example 1

Replace the [=](https://dax.guide/op/equal-to/) operator with [==](https://dax.guide/op/strictly-equal-to/) or use [[ISBLANK]] to get the right behavior and better performance.

### Original code

```dax
CALCULATE (
    [Sales Amount], 
    Product[Brand] = BLANK()
)
```

### Possible optimization 1

```dax
CALCULATE (
    [Sales Amount], 
    Product[Brand] == BLANK()
)
```

### Possible optimization 2

```dax
CALCULATE (
    [Sales Amount], 
    ISBLANK ( Product[Brand] )
)
```

## Example 2

Remove the [<>](https://dax.guide/op/not-equal-to/) operator and use [NOT](https://dax.guide/op/not/) [[ISBLANK]] to get the right behavior and better performance.

### Original code

```dax
CALCULATE (
    [Sales Amount], 
    Product[Brand] <> BLANK()
)
```

### Possible optimization

```dax
CALCULATE (
    [Sales Amount], 
    NOT ISBLANK ( Product[Brand] )
)
```
