---
title: "Function replace FIRSTDATE/LASTDATE with MIN/MAX"
kb_id: "102800"
url: "https://kb.daxoptimizer.com/d/102800"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Function replace FIRSTDATE/LASTDATE with MIN/MAX

When [[FIRSTDATE]] or [[LASTDATE]] functions are used to return a scalar value instead of a table, the same result can be obtained in a more efficient way by using [[MIN]] or [[MAX]] on the column.

## Remarks

[[FIRSTDATE]] and [[LASTDATE]] are table functions that internally use [[MIN]] and [[MAX]], respectively.

Whenever the expression requires a scalar value, using the table function is unnecessarily expensive, also because it could involve a context transition in case one or more row contexts are active.

Recommended articles:

- [Semi-Additive Measures in DAX](https://www.sqlbi.com/articles/semi-additive-measures-in-dax/)

## Example 1

Replace [[FIRSTDATE]] with [[MIN]].

### Original code

```dax
VAR firstDate = FIRSTDATE( Sales[Order Date] )
```

### Possible optimization

```dax
VAR firstDate = MIN( Sales[Order Date] )
```

## Example 2

Replace [[LASTDATE]] with [[MAX]].

### Original code

```dax
CALCULATE (
    LASTDATE( Sales[Order Date] ),
    Customer[Country] = "Canada"
)
```

### Possible optimization

```dax
CALCULATE (
    MAX( Sales[Order Date] ),
    Customer[Country] = "Canada"
)
```
