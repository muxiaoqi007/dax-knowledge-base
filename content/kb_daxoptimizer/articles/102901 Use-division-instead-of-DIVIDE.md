---
title: "Use division instead of DIVIDE"
kb_id: "102901"
url: "https://kb.daxoptimizer.com/d/102901"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Use division instead of DIVIDE

When [[DIVIDE]] is used within an iterator, it’s possible to modify the filter context to exclude rows where the column used as the denominator is zero and replaced [[DIVIDE]] with the native [division operator](https://dax.guide/operators/).

## Remarks

[[DIVIDE]] always forces the Formula Engine to evaluate the expression and, in many scenarios, this may results in poor Storage Engine cache usage and worse performance compared to a native [division operator](https://dax.guide/operators/).

Recommended articles:

- [DIVIDE Performance](https://www.sqlbi.com/articles/divide-performance/)

## Example 1

Replace [[DIVIDE]] with `/` [division operator](https://dax.guide/operators/) and use [[CALCULATE]] to modify the filter context to exclude rows where the column used as the denominator is zero.

### Original code

```dax
SUMX ( 
    Sales, 
    DIVIDE ( Sales[Net Price], Sales[Quantity] )
)
```

### Possible optimization

```dax
CALCULATE (
    SUMX ( 
        Sales, 
        Sales[Net Price] / Sales[Quantity]
    ),
    KEEPFILTERS ( Sales[Quantity] <> 0 )
)
```
