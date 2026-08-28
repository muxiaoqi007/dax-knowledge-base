---
title: "Duplicated measure"
kb_id: "100200"
url: "https://kb.daxoptimizer.com/d/100200"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Duplicated measure

There are multiple references to the same measure executed with an identical filter context.

## Remarks

Two measure references can return different results when executed in different filter contexts, but within the same filter context the result should be the same. However, the formula engine might perform redundant evaluations of the same expressions even though the storage engine usually does not execute duplicated requests.

A possible optimization is to store the result of a measure reference in a variable, and then reference the variable. It is important to assign the variable only when the execution scope guarantees that the variable is used at least once, otherwise the result could be counterproductive and slow down the execution time instead of improving it.

## Example 1

**Evaluate the measure only once and assign it to a variable.** If the measure *ExchangeRate* does not depend on the rows iterated in *Sales*, compute the Exchange Rate in a variable before starting the iterator.

### Original code

```dax
IF ( 
    [Sales Quantity] > 0 && [Sales Amount] > 0, 
    [Sales Quantity] / [Sales Amount]
)
```

### Possible optimization

```dax
VAR _quantity = [Sales Quantity]
VAR _amount = [Sales Amount]
RETURN
    IF ( 
        _quantity > 0 && _amount > 0, 
        _quantity / _amount
    )
```

## Example 2

**Evaluate the measure only once in and assign it to a variable in a proper scope.** *Sales Quantity* is evaluated twice in the FILTER iterator and can be computed only once in a variable defined in the same iterator. *Sales Quantity* before the iterator is evaluated only once in a different scope and does not require a variable.

### Original code

```dax
IF (
    [Sales Quantity] > 0,
    COUNTROWS ( 
        FILTER (
            Customer,
            [Sales Quantity] > 0
                && [Sales Quantity] <= 5,
        )
    )
)
```

### Possible optimization

```dax
IF (
    [Sales Quantity] > 0,
    COUNTROWS ( 
        FILTER (
            Customer,
            VAR _quantity = [Sales Quantity]
            RETURN
                _quantity > 0
                    && _quantity <= 5,
        )
    )
)
```
