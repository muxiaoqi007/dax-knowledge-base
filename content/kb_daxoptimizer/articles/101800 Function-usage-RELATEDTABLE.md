---
title: "Function usage RELATEDTABLE"
kb_id: "101800"
url: "https://kb.daxoptimizer.com/d/101800"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Function usage RELATEDTABLE

Checks whether the [[RELATEDTABLE]] function is not needed as there is no active row context.

## Remarks

If the [[RELATEDTABLE]] function is invoked without an active row context, it does not perform any action. Even though it does not impact performance because there is no context transition involved, it is better to remove the function to make the code easier to read, or re-evaluate the code structure in case the context transition was needed.

## Example

Remove [[RELATEDTABLE]] as it is redundant and it does not produce any effect.

### Original code

```dax
Canada Sales :=
CALCULATE (
    [Sales Amount],
    FILTER ( 
        RELATEDTABLE (
            Customer
        ),
        Customer[Country] = "Canada"
    )
)
```

### Possible optimization

```dax
Canada Sales :=
CALCULATE (
    [Sales Amount],
    FILTER ( 
        Customer,
        Customer[Country] = "Canada"
    )
)
```
