---
title: "Context transition in iterator"
kb_id: "100100"
url: "https://kb.daxoptimizer.com/d/100100"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Context transition in iterator

A [context transition](https://www.sqlbi.com/articles/understanding-context-transition-in-dax/) is executed within an iterator with an active row context.

## Remarks

Whenever a row context is transformed to a filter context, such as when calling [[CALCULATE]], [[CALCULATETABLE]], etc. or when referencing a measure, a context transition occurs, in which the values within all columns of the active row context gets transformed into a corresponding set of filters. This operation can be expensive - especially when many columns have to be transformed, or when the operation needs to be performed many times because of an outside iteration.

**There is no general solution to the problem.** Investigate whether you can move a calculation outside the iteration by storing it in a variable, or if you can reduce the cardinality of the iteration. For example, in currency conversion scenarios, there is no need to iterate all transactions. Instead, just iterate the list of unique exchange rates, which should have a much lower cardinality.

## Example 1

**Move the measure reference outside of the iterator.** If the measure *ExchangeRate* does not depend on the rows iterated in *Sales*, compute the *\_ExchangeRate* in a variable before starting the iterator. This optimization is not possible if the context transition affects the result of the measure: in that case, look at other examples.

### Original code

```dax
SUMX (
    Sales, 
    Sales[Line Amount] * [ExchangeRate]
)
```

### Possible optimization

```dax
VAR _ExchangeRate = [ExchangeRate]
RETURN
    SUMX (
        Sales, 
        Sales[Line Amount] * _ExchangeRate
    )
```

## Example 2

**Move the expression within CALCULATE outside of the iterator.** If the expression *AVERAGE ( ‘Currency Exchange’[Exchange] )* does not depend on the rows iterated in *Sales*, compute the expression in a variable before starting the iterator. This optimization is not possible if the context transition affects the result of the expression: in that case, look at other examples.

### Original code

```dax
SUMX (
    Sales, 
    Sales[Line Amount] * CALCULATE ( AVERAGE ( 'Currency Exchange'[Exchange] ) )
)
```

### Possible optimization

```dax
VAR _ExchangeRate = CALCULATE ( AVERAGE ( 'Currency Exchange'[Exchange] ) )
RETURN
    SUMX (
        Sales, 
        Sales[Line Amount] * _ExchangeRate
    )
```

## Example 3

**Reduce the cardinality of the iterator.** If the measure *ExchangeRate* should not change the value for each row of *Sales* with the same date, modify the code so that the calculation of the *ExchangeRate* measure happens for each day and not for each transaction within the same date.

### Original code

```dax
SUMX (
    Sales, 
    Sales[Line Amount] * [ExchangeRate]
)
```

### Possible optimization

```dax
SUMX (
    VALUES ( Sales[Order Date] ), 
    CALCULATE ( SUM ( Sales[Line Amount] ) ) * [ExchangeRate]
)
```

## Example 4

**Reduce the cardinality of the iterator.** If the measure *Sales Amount* is additive and is used in a [[SUMX]] iterator with a multiplication, you can reduce the iterator cardinality by iterating only the unique values of the column(s) referenced in the aggregated expression. For example, you you multiple the *Discount %* of each customer by the *Sales Amount* of the customer, you can obtain the same result by iterating just the *Discount %* unique values.

### Original code

```dax
SUMX (
    Customer,
    [Sales Amount] * Customer[Discount %]
)
```

### Possible optimization

```dax
SUMX (
    VALUES ( Customer[Discount %] ), 
    [Sales Amount] * Customer[Discount %]
)
```
