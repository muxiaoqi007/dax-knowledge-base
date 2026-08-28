---
title: "Function replace DISTINCTCOUNT/HASONEVALUE"
kb_id: "101400"
url: "https://kb.daxoptimizer.com/d/101400"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Function replace DISTINCTCOUNT/HASONEVALUE

[[DISTINCTCOUNT]] function can be replaced by [[HASONEVALUE]].

## Remarks

The code that compares the result of [[DISTINCTCOUNT]] with 1 can be replaced with the function [[HASONEVALUE]].
This recommendation does not have a relevant impact on performance, but it is a best practice that improves code readability.

## Example

Replace [[DISTINCTCOUNT]] and the comparison with [[HASONEVALUE]].

### Original code

```dax
IF (
    DISTINCTCOUNT ( Customer[CustomerKey] ) = 1,
    "Customer Name : " & Customer[Name],
    "Undefined"
)
```

### Possible optimization

```dax
IF (
    HASONEVALUE ( Customer[CustomerKey] ),
    "Customer Name : " & Customer[Name],
    "Undefined"
)
```
