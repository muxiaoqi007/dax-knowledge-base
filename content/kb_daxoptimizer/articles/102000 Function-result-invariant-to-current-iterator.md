---
title: "Function result invariant to current iterator"
kb_id: "102000"
url: "https://kb.daxoptimizer.com/d/102000"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Function result invariant to current iterator

The function within an expression argument of an iterator function does not use the iterator’s row context and can therefore be optimized using a variable.

## Remarks

The part of an expression executed in a row context that do not depend on the row context might require redundant evaluations of the formula engine, resulting in additional CPU cost. In this case, the function call is identified as part of such redundant expression.

A possible optimization is to store the result of the function call in a variable, and then reference the variable. It is important to assign the variable only when the execution scope guarantees that the variable is used at least once, otherwise the result could be counterproductive and slow down the execution time instead of improving it.

## Example

**Evaluate the measure before the iterator and assign it to a variable.** The function [[YEAR]] depends on a variable assigned before the [[FILTER]] iterator, so also [[YEAR]] can be evaluated before [[FILTER]].

### Original code

```dax
VAR _LastDate = LASTDATE()
VAR _FilterYear =
    FILTER (
        'Date',
        'Date'[Year] = YEAR ( _LastDate )
    )
RETURN
    CALCULATE (
        [Sales Amount],
        _FilterYear
    )
```

### Possible optimization

```dax
VAR _LastDate = LASTDATE()
VAR _Year = YEAR ( _LastDate )
VAR _FilterYear =
    FILTER (
        'Date',
        'Date'[Year] = _Year
    )
RETURN
    CALCULATE (
        [Sales Amount],
        _FilterYear
    )
```
