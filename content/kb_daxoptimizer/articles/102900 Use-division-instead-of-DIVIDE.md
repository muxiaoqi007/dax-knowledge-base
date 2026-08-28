---
title: "Use division instead of DIVIDE"
kb_id: "102900"
url: "https://kb.daxoptimizer.com/d/102900"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Use division instead of DIVIDE

When the denominator is a non-zero constant, [[DIVIDE]] can be replaced with the native [division operator](https://dax.guide/operators/).

## Remarks

[[DIVIDE]] always forces the Formula Engine to evaluate the expression and, in many scenarios, this may results in poor Storage Engine cache usage and worse performance compared to a native [division operator](https://dax.guide/operators/).

Recommended articles:

- [DIVIDE Performance](https://www.sqlbi.com/articles/divide-performance/)

## Example 1

Replace [[DIVIDE]] with `/` [division operator](https://dax.guide/operators/).

### Original code

```dax
SUMX ( 
    Sales, 
    DIVIDE ( Sales[Quantity], 3 )
)
```

### Possible optimization

```dax
SUMX ( 
    Sales, 
    Sales[Quantity] / 3
)
```
