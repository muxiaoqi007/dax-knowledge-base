---
title: "Function usage (LAST/FIRST)NONBLANK(VALUES)"
kb_id: "101200"
url: "https://kb.daxoptimizer.com/d/101200"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Function usage (LAST/FIRST)NONBLANK(VALUES)

The use of [[FIRSTNONBLANK]], [[LASTNONBLANK]], [[FIRSTNONBLANKVALUE]], and [[LASTNONBLANKVALUE]] is not optimal for performance.

## Remarks

The “NONBLANK” functions are all iterators that evaluate an expression for each row of the table iterated, just to check whether the result is blank or not. When a column reference is provided as the first argument, all the values in the filter context for that column are iterated.

Most of the times, these functions are used primarily to retrieve the last date when there is a transaction in a table in the current filter context. The same result can be obtained in a more efficient way by using [[MIN]] or [[MAX]] on the column, as it removes the need of a more complex calculation that usually requires also a context transition.

The unusual case when it is not possible to optimize the code is when the blank result of the expression does not depend only on the presence of data corresponding to the row context of the iterator, like a *Revenues* measure that can return BLANK even though there are transaction in the aggregated *Sales* table.

These functions can be removed by rewriting the expression as suggested in the following article:

- [Optimizing LASTNONBLANK and LASTNONBLANKVALUE calculations](https://www.sqlbi.com/articles/optimizing-lastnonblank-and-lastnonblankvalue-calculations/)

## Example 1

Store in a variable the result of [[MAX]] and use that value to filter *‘Date’[Date]* instead of using [[LASTNONBLANK]].

### Original code

```dax
CALCULATE (
    [Balance Amount],
    LASTNONBLANK ( 'Date'[Date], [Balance Amount] )
)
```

### Possible optimization

```dax
VAR _LastDate = MAX ( 'Date'[Date] )
RETURN
    CALCULATE (
        [Balance Amount],
        'Date'[Date] = _LastDate
    )
```

## Example 2

Remove [[LASTNONBLANKVALUE]], store in a variable the result of [[MAX]] and use that value to filter *‘Date’[Date]* in a [[CALCULATE]] function.

### Original code

```dax
LASTNONBLANKVALUE ( 
    'Date'[Date],
    [Balance Amount]
)
```

### Possible optimization

Both expressions in the original code can be replaced by using the following implementation.

```dax
VAR _LastDate = MAX ( 'Date'[Date] )
RETURN
    CALCULATE (
        [Balance Amount],
        'Date'[Date] = _LastDate
    )
```

## Example 3

Store in a variable the result of [[MIN]] and use that value to filter *‘Date’[Date]* instead of using [[FIRSTNONBLANK]].

### Original code

```dax
CALCULATE (
    [Balance Amount],
    FIRSTNONBLANK ( 'Date'[Date], [Balance Amount] )
)
```

### Possible optimization

```dax
VAR _FirstDate = MIN ( 'Date'[Date] )
RETURN
    CALCULATE (
        [Balance Amount],
        'Date'[Date] = _FirstDate
    )
```

## Example 4

Remove [[FIRSTNONBLANKVALUE]], store in a variable the result of [[MIN]] and use that value to filter *‘Date’[Date]* in a [[CALCULATE]] function.

### Original code

```dax
FIRSTNONBLANKVALUE ( 
    'Date'[Date],
    [Balance Amount]
)
```

### Possible optimization

Both expressions in the original code can be replaced by using the following implementation.

```dax
VAR _FirstDate = MIN ( 'Date'[Date] )
RETURN
    CALCULATE (
        [Balance Amount],
        'Date'[Date] = _FirstDate
    )
```
