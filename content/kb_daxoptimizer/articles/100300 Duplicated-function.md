---
title: "Duplicated function"
kb_id: "100300"
url: "https://kb.daxoptimizer.com/d/100300"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Duplicated function

There are multiple executions of the same DAX function with the same arguments.

## Remarks

If a function is called with the same arguments within the same filter context, it returns the same value. However, the formula engine may perform redundant evaluations, resulting in additional CPU cost.

A possible optimization is to store the result of a function call in a variable, and then reference the variable. It is important to assign the variable only when the execution scope guarantees that the variable is used at least once, otherwise the result could be counterproductive and slow down the execution time instead of improving it.

There are functions that do not depend on the filter context for the result they produce, so the optimization is possible for those functions even though they are executed in different filter contexts. For example, [[LASTDATE]] and [[SUM]] depend on the filter context, whereas [[TRUNC]] and [[INT]] do not depend depend on the filter context but only on the value passed to them.

## Example

**Evaluate the function only once and assign it to a variable.** If the measure *ExchangeRate* does not depend on the rows iterated in *Sales*, compute the Exchange Rate in a variable before starting the iterator.

### Original code

```dax
VAR _LastBalance =
    CALCULATE ( 
        [Balance],
        LASTDATE ( 'Date'[Date] )
    )
VAR _LastExpenses =
    CALCULATE ( 
        [Amount],
        Account[Type] = "Expenses",
        LASTDATE ( 'Date'[Date] )
    )
RETURN LastBalance - LastExpenses
```

### Possible optimization

```dax
VAR _LastDate = LASTDATE ( 'Date'[Date] )
VAR _LastBalance =
    CALCULATE ( 
        [Balance],
        _LastDate
    )
VAR _LastExpenses =
    CALCULATE ( 
        [Amount],
        Account[Type] = "Expenses",
        _LastDate
    )
RETURN LastBalance - LastExpenses
```
