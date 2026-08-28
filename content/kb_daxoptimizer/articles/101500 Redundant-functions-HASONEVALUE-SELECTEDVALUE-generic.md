---
title: "Redundant functions HASONEVALUE/SELECTEDVALUE (generic)"
kb_id: "101500"
url: "https://kb.daxoptimizer.com/d/101500"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Redundant functions HASONEVALUE/SELECTEDVALUE (generic)

The expression involving [[HASONEVALUE]] and [[SELECTEDVALUE]] functions can be optimized by removing [[HASONEVALUE]].

## Remarks

[[SELECTEDVALUE]] returns a non-blank value when there is only one value visible in the referenced column. Embedding [[SELECTEDVALUE]] in a condition related to [[HASONEVALUE]] can be redundant: removing [[HASONEVALUE]] can slightly improve the performance. The code must be refactored so that it returns the same value as the original code, which might be not possible in certain scenarios.

## Example

Remove the outer [[IF]] function and the [[HASONEVALUE]] condition.

### Original code

```dax
IF (
    HASONEVALUE( Customer[Country] ),
    IF (
        SELECTEDVALUE( Customer[Country] ) = "Canada",
        [Sales Amount]
    )
)
```

### Possible optimization

```dax
IF (
    SELECTEDVALUE( Customer[Country] ) = "Canada",
    [Sales Amount]
)
```
