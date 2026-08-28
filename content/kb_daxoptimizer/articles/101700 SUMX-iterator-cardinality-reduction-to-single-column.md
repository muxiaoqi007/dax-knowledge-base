---
title: "SUMX iterator cardinality reduction to single column"
kb_id: "101700"
url: "https://kb.daxoptimizer.com/d/101700"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# SUMX iterator cardinality reduction to single column

Investigate if the cardinality of the iterated table can be reduced by iterating a single column.

## Remarks

The [[SUMX]] function iterates an entire table, but the expression evaluated for each row seems related to a single column of the table with a product operation that requires a context transition.

If the result of the measure is additive and consistent even when the context transition has only one value of the column instead of a row of the table, then the iterator cardinality can be reduced to the unique values of the column instead of iterating the entire table. Use [[DISTINCT]] to iterate the column values to be consistent with a table reference, or use [[VALUES]] to include the blank row if needed for the correct evaluation.

## Example

Replace the *Customer* table reference with [[DISTINCT]] on *Customer[Discount]*.

### Original code

```dax
SUMX ( 
    Customer, 
    Customer[Discount] * [Sales Amount] 
)
```

### Possible optimization

```dax
SUMX ( 
    DISTINCT ( Customer[Discount] ), 
    Customer[Discount] * [Sales Amount] 
)
```
