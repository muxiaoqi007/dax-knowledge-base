---
title: "Redundant functions HASONEVALUE/SELECTEDVALUE, duplicated expression in arg. 2 of inner IF and arg. 3 outer IF)"
kb_id: "101502"
url: "https://kb.daxoptimizer.com/d/101502"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Redundant functions HASONEVALUE/SELECTEDVALUE, duplicated expression in arg. 2 of inner IF and arg. 3 outer IF)

The expression involving [[HASONEVALUE]] and [[SELECTEDVALUE]] functions can be optimized by removing [[HASONEVALUE]], reducing the redundant expression in the second argument of the inner [[IF]] function and the third argument of the outer [[IF]] function.

## Remarks

[[SELECTEDVALUE]] returns a non-blank value when there is only one value visible in the referenced column. Embedding [[SELECTEDVALUE]] in a condition related to [[HASONEVALUE]] seems redundant: removing [[HASONEVALUE]] can slightly improve the performance.

The third argument of both [[IF]] functions is duplicated, which allows to rely on the inner [[IF]] functions also for the cases covered by the outer [[IF]] function.

## Example

Remove the outer [[IF]] function, the [[HASONEVALUE]] condition, and the third argument of the outer [[IF]] function. Assign [[VALUES]] to a variable, use [[COUNTROWS]] to count the values returned and use a single condition with these variables to check whether the common expression *Sales Amount* should be returned.

### Original code

```dax
IF (
    HASONEVALUE( Customer[Country] ),
    IF (
        SELECTEDVALUE( Customer[Country] ) = "Canada",
        [Sales Amount],
        BLANK()
    ),
    [Sales Amount]
)
```

### Possible optimization

```dax
VAR _SelectedValues = VALUES ( Customer[Country] )
VAR _NumberOfSelectedValues = COUNTROWS ( _SelectedValues )
RETURN
    IF ( 
        (_NumberOfSelectedValues = 1 && _SelectedValues = "Canada")
            || _NumberOfSelectedValues <> 1,
        [Sales Amount],
        BLANK ()
    )
```
