---
title: "SUMX iterator excessive CallbackDataId"
kb_id: "101900"
url: "https://kb.daxoptimizer.com/d/101900"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# SUMX iterator excessive CallbackDataId

Avoid excessive CallbackDataId calls by reducing iterator cardinality.

## Remarks

The presence of a function with a column reference iterating a large table can produce a large number of calls to the formula engine with the same values. With a [[SUMX]] iterator and an expression that computes a product with an additive measure, it could be possible to reduce the cardinality of the iterator and produce the same result. The benefit is a reduced number of callbacks to the formula engine, which results in better performance.

## Example 1

Store in a variable the [[SUM]] of *Sales[Line Amount]* for each *Sales[Order Date]*, then iterate the variable and compute the product by *ExchangeRate[Rate]* calling [[LOOKUPVALUE]] for each *Sales[Order Date]* instead of each row in *Sales*.

### Original code

```dax
SUMX (
    Sales,
    Sales[LineAmount]
        * LOOKUPVALUE (
            ExchangeRates[Rate],
            ExchangeRates[Date], Sales[Order Date]
        )
)
```

### Possible optimization

```dax
VAR _AggregateAtGranularity = 
    ADDCOLUMNS ( 
        VALUES ( Sales[Order Date] ),    
        "@Amount", CALCULATE ( SUM ( Sales[LineAmount] ) ) 
    )
VAR Result =
    SUMX (
        _AggregateAtGranularity,
        [@Amount]
            * LOOKUPVALUE (
                ExchangeRates[Rate],
                ExchangeRates[Date], Sales[Order Date]
            )
    )
RETURN Result
```

## Example 2

Store in a variable the [[SUM]] of *Sales[Line Amount]* for each *Date[Year]* that is referenced in *Sales*, then iterate the variable and compute the product by *Rates[InflationRate]* calling [[LOOKUPVALUE]] for each *Date[Year]* instead of each row in *Sales*.

### Original code

```dax
SUMX (
    Sales,
    Sales[LineAmount]
        * LOOKUPVALUE (
            Rates[InflationRate],
            Rates[Year], RELATED ( 'Date'[Year] )
        )
)
```

### Possible optimization

```dax
VAR _GroupByDependency = 
    SUMMARIZE (
        Sales, 
        'Date'[Year]
    )
VAR _AggregateAtGranularity = 
    ADDCOLUMNS ( 
        _GroupByDependency,    
        "@Amount", CALCULATE ( SUM ( Sales[LineAmount] ) ) 
    )
VAR Result =
    SUMX (
        _AggregateAtGranularity,
        [@Amount]
            * LOOKUPVALUE (
                Rates[InflationRate],
                Rates[Year], 'Date'[Year]
            )
    )
RETURN Result
```
