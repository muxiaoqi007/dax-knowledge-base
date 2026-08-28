---
title: "Function replace GROUPBY/SUMMARIZE"
kb_id: "100700"
url: "https://kb.daxoptimizer.com/d/100700"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Function replace GROUPBY/SUMMARIZE

The GROUPBY function can be replaced by [[SUMMARIZE]].

## Remarks

The [[GROUPBY]] function should be used only when the table cannot be used in [[SUMMARIZE]] or when there are specific features of [[GROUPBY]] that are necessary for the calculation. Whenever possible, use [[SUMMARIZE]] instead of [[GROUPBY]].

## Example

Replace [[GROUPBY]] with [[SUMMARIZE]].

### Original code

```dax
SUMX (
    GROUPBY (
        'Sales',
        'Sales'[ProductKey],
        'Sales'[CustomerKey],
        'Sales'[DateKey]
    ),
    [Sales Amount]
)
```

### Possible optimization

```dax
SUMX (
    SUMMARIZE (
        'Sales',
        'Sales'[ProductKey],
        'Sales'[CustomerKey],
        'Sales'[DateKey]
    ),
    [Sales Amount]
)
```
