---
title: "Filtered table as iterator"
kb_id: "100400"
url: "https://kb.daxoptimizer.com/d/100400"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Filtered table as iterator

A filtered table is used as an iterator instead of modifying the filter context.

## Remarks

An iterator over the result of [[FILTER]] results in nested iterators, which are not ideal for an optimal query plan. Whenever possible, the table iterated by [[FILTER]] should be filtered through the filter context, moving the filter condition(s) in [[CALCULATE]] filter argument(s).

## Example 1

**Move the filter condition in a CALCULATE filter argument.** Remove the [[FILTER]] iterator and wrap [[SUMX]] into a [[CALCULATE]], placing the filter condition into a filter argument wrapped into [[KEEPFILTERS]] to maintain the original semantics.

### Original code

```dax
SUMX (
    FILTER ( 
        Sales, 
        RELATED ( Customer[Country] ) = "Canada"
    ),
    Sales[Line Amount]
)
```

### Possible optimization

```dax
CALCULATE (
    SUMX (
        Sales,
        Sales[Line Amount]
    ),
    KEEPFILTERS ( Customer[Country] = "Canada" )
)
```

## Example 2

**Move the filter conditions evaluated by an AND operator in two CALCULATE filter arguments.** Remove the [[FILTER]] iterator and wrap [[SUMX]] into a [[CALCULATE]], splitting the [&&](https://dax.guide/op/and/) arguments in two filter arguments, each one wrapped into [[KEEPFILTERS]] to maintain the original semantics.

### Original code

```dax
SUMX (
    FILTER ( 
        Sales, 
        RELATED ( Customer[Country] ) = "Canada"
            && RELATED ( Customer[City] ) = "Vancouver"
    ),
    Sales[Line Amount]
)
```

### Possible optimization

```dax
CALCULATE (
    SUMX (
        Sales,
        Sales[Line Amount]
    ),
    KEEPFILTERS ( Customer[Country] = "Canada" ),
    KEEPFILTERS ( Customer[City] = "Vancouver" )
)
```

## Example 3

**Move the filter conditions evaluated by an OR operator in one CALCULATE filter argument.** Remove the [[FILTER]] iterator and wrap [[SUMX]] into a [[CALCULATE]], placing the filter condition based on an [||](https://dax.guide/op/or/) operator into a single filter argument wrapped into [[KEEPFILTERS]] to maintain the original semantics.

### Original code

```dax
SUMX (
    FILTER ( 
        Sales, 
        RELATED ( Customer[Country] ) = "Canada"
            || RELATED ( Customer[City] ) = "Seattle"
    ),
    Sales[Line Amount]
)
```

### Possible optimization

```dax
CALCULATE (
    SUMX (
        Sales,
        Sales[Line Amount]
    ),
    KEEPFILTERS ( 
        Customer[Country] = "Canada"
            || Customer[City] = "Seattle"
    )
)
```
