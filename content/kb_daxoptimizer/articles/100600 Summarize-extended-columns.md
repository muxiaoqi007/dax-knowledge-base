---
title: "Summarize extended columns"
kb_id: "100600"
url: "https://kb.daxoptimizer.com/d/100600"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Summarize extended columns

Extended columns have been added within [[SUMMARIZE]] instead of using [[ADDCOLUMNS]] for the aggregations and [[SUMMARIZE]] only for the grouping columns.

## Remarks

The [[SUMMARIZE]] behavior for aggregations may produce slower performances compared to an equivalent construct where [[ADDCOLUMNS]] extends the result of [[SUMMARIZE]] by adding the columns with the calculations.

Recommended articles:

- [Best practices using SUMMARIZE and ADDCOLUMNS](https://www.sqlbi.com/articles/best-practices-using-summarize-and-addcolumns/)
- [All the secrets of SUMMARIZE](https://www.sqlbi.com/articles/all-the-secrets-of-summarize/)

## Example

Embed SUMMARIZE in an ADDCOLUMNS function moving there the aggregation expressions and the corresponding columns. Add CALCULATE for aggregations to get the equivalent filter context for the argument.

### Original code

```dax
SUMMARIZE (
    Sales,
    Customer[Country], 
    'Product'[Brand], 
    "Revenues", [Sales Amount],
    "Transactions", COUNTROWS ( Sales ),
    "Short Country", LEFT ( Customer[Country], 3 )
)
```

### Possible optimization

```dax
ADDCOLUMNS (
    SUMMARIZE (
        Sales,
        Customer[Country], 
        'Product'[Brand]
    ), 
    "Revenues", [Sales Amount],
    "Transactions", CALCULATE ( COUNTROWS ( Sales ) ),
    "Short Country", LEFT ( Customer[Country], 3 )
)
```
