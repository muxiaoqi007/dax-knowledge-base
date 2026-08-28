---
title: "Use subtraction instead of DATEDIFF with DAY interval"
kb_id: "103000"
url: "https://kb.daxoptimizer.com/d/103000"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Use subtraction instead of DATEDIFF with DAY interval

When [[DATEDIFF]] is used with the DAY interval, performance can be improved by using the native [subtraction operator](https://dax.guide/op/subtraction/) between the two dates, removing unnecessary callbacks to the formula engine.

## Remarks

This optimization is applicable when the [[DATEDIFF]] function is used with the DAY interval.

### ⚠️ IMPORTANT

The difference in days is the same if the two values do not have a different time component. It is a best practice to split date and time into two different columns, so that their processing is more efficient.
If you have a timestamp in one column, then the result of the difference is different than the result using [[DATEDIFF]], and you cannot apply this optimization.

However, it is strongly suggested to review the data model and create a date column (without the time part) to make the storage and the calculation more efficient, also by replacing [[DATEDIFF]] with a subtraction operation at that point.

## Example 1

**Replace [[DATEDIFF]] with the native [subtraction operator](https://dax.guide/op/subtraction/) to calculate the difference in days between two dates.** Remove the [[DATEDIFF]] function and the DAY parameter. Invert the order of the dates and use a direct subtraction `-` between the end date and the start date.

### Original code

```dax
SUMX ( 
    Sales, 
    DATEDIFF ( Sales[Order Date], Sales[Ship Date], DAY )
)
```

### Possible optimization

```dax
SUMX ( 
    Sales, 
    Sales[Ship Date] - Sales[Order Date]
)
```
