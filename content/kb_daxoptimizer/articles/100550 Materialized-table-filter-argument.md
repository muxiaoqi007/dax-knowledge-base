---
title: "Materialized table filter argument"
kb_id: "100550"
url: "https://kb.daxoptimizer.com/d/100550"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Materialized table filter argument

A physical table or a table expression function based on a physical table was used as a table filter argument in functions such as [[CALCULATE]], [[CALCULATETABLE]], etc.

## Remarks

Instead of materializing an entire table in the filter context, it is better to only filter the columns involved.

By removing the table, it is important to **keep the same semantics as the original filter**, so that the result is not affected. To do that, apply [[REMOVEFILTERS]] or [[KEEPFILTERS]] as needed.

Recommended articles:

- [Filter arguments in CALCULATE](https://www.sqlbi.com/articles/filter-arguments-in-calculate/)
- [Using KEEPFILTERS](https://www.sqlbi.com/articles/using-keepfilters-in-dax-updated/)

## Example 1

Remove the *Sales* iterator and use [[KEEPFILTERS]] around the condition to maintain the same semantics, so the filter is applied only to the *Sales[Unit Price]* column specified in the predicate instead of using the entire *Sales* table.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    FILTER ( Sales, Sales[Unit Price] > 100 )
)
```

### Possible optimization

```dax
CALCULATE (
    [Sales Amount],
    KEEPFILTERS ( Sales[Unit Price] > 100 )
)
```

## Example 2

Remove the *Date* iterator and add [[REMOVEFILTERS]] on *Date* so that the filter context is removed from all the other columns of the *Date* table to keep the original semantics once the condition only filters *Date[Year]*.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    FILTER ( 
        ALL ( 'Date' ),
        'Date'[Year] = YEAR ( TODAY() ) 
    )
)
```

### Possible optimization

```dax
CALCULATE (
    [Sales Amount],
    REMOVEFILTERS ( 'Date' ),
    'Date'[Year] = YEAR ( TODAY() )
)
```

## Example 3

Remove the *Sales* iterator and use [[KEEPFILTERS]] around the condition to maintain the same semantics, so the filter is applied only to the two *Sales[Quantity]* and *Sales[Unit Price]* columns specified in the predicate instead of using the entire *Sales* table.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    FILTER ( Sales, Sales[Quantity] * Sales[Unit Price] > 1000 )
)
```

### Possible optimization

```dax
CALCULATE (
    [Sales Amount],
    KEEPFILTERS ( Sales[Quantity] * Sales[Unit Price] > 1000 )
)
```

## Example 4

Remove the *Customer* iterator and use [[KEEPFILTERS]] around the condition to maintain the same semantics, so the filter is applied only to the two *Customer[Country]* and *Customer[State]* columns specified in the predicate instead of using the entire *Customer* table.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    FILTER ( 
        Customer,
        Customer[Country] = "Canada" 
            || (Customer[Country] = "United States" && Customer[State] = "Washington" )
    )
)
```

### Possible optimization

```dax
CALCULATE (
    [Sales Amount],
    KEEPFILTERS ( 
        Customer[Country] = "Canada" 
            || (Customer[Country] = "United States" && Customer[State] = "Washington" )
    )
)
```

## Example 5

Remove the *Sales* iterator and the [[RELATED]] function because the column filter does not need it. Use [[KEEPFILTERS]] around the condition to maintain the same semantics, so the filter is applied only to the *Customer[Country]* column specified in the predicate instead of using the entire *Sales* expanded table, which would include also the entire *Customer* table.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    FILTER ( Sales, RELATED ( Customer[Country] ) = "Canada" )
)
```

### Possible optimization

```dax
CALCULATE (
    [Sales Amount],
    KEEPFILTERS (  Customer[Country] = "Canada" )
)
```

## Example 6

Remove the *Sales* iterator and the [[RELATED]] function because the column filter does not need it. Also split the [&&](https://dax.guide/op/and/) operator between two columns into two single-column filters. Use [[KEEPFILTERS]] around each condition to maintain the same semantics, so the filter is applied only to the *Customer[Country]* and *Sales[Unit Price]* columns specified in the predicate instead of using the entire *Sales* expanded table, which would include also the entire *Customer* table.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    FILTER ( 
        Sales, 
        RELATED ( Customer[Country] ) = "Canada" 
            && Sales[Unit Price] > 100 
    )
)
```

### Possible optimization

```dax
CALCULATE (
    [Sales Amount],
    KEEPFILTERS ( Customer[Country] = "Canada" ),
    KEEPFILTERS ( Sales[Unit Price] > 100 )
)
```

## Example 7

Remove the *Sales* iterator and the [[RELATED]] function because the column filter does not need it. The [||](https://dax.guide/op/or/) operator cannot be split into two filters, and because it uses columns on two different table, it must be obtained by filtering a table with the two columns. Use [[KEEPFILTERS]] around the final filter to maintain the same semantics, so the filter is applied only to the *Customer[Country]* and *Sales[Unit Price]* columns specified in the predicate instead of using the entire *Sales* expanded table, which would include also the entire *Customer* table.

### Original code

```dax
CALCULATE (
    [Sales Amount],
    FILTER ( 
        Sales, 
        RELATED ( Customer[Country] ) = "Canada"
            || Sales[Unit Price] > 100 
    )
)
```

### Possible optimization (v1)

Use [[CROSSJOIN]] to create the filter if the two columns have a low cardinality and the table *Sales* is very large.

```dax
VAR _ComplexFilter =
    FILTER (
        CROSSJOIN ( 
            ALL ( Customer[Country] ),
            ALL ( Sales[Unit Price] ),
        ),
        Customer[Country] = "Canada" 
            || Sales[Unit Price] > 100
    )
RETURN
    CALCULATE (
        [Sales Amount],
        KEEPFILTERS ( _ComplexFilter )
    )
```

### Possible optimization (v2)

Use [[SUMMARIZE]] to create the filter if the two columns have a large cardinality and the existing combinations used in *Sales* are a small fraction of the cartesian product of the two columns.

```dax
VAR _ComplexFilter =
    FILTER (
        CALCULATETABLE ( 
            SUMMARIZE ( Sales, Customer[Country], Sales[Unit Price] ),
            REMOVEFILTERS ( Sales )
        ),
        Customer[Country] = "Canada" 
            || Sales[Unit Price] > 100 
    )
RETURN
    CALCULATE (
        [Sales Amount],
        KEEPFILTERS ( _ComplexFilter )
    )
```
