---
title: "SUMX with IF predicate on iterator column"
kb_id: "101600"
url: "https://kb.daxoptimizer.com/d/101600"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# SUMX with IF predicate on iterator column

The [[SUMX]] function contains an [[IF]] predicate on a single column filtering the iterated table.

## Remarks

The [[IF]] condition in an iterator can be expensive from a performance standpoint. When the condition only references columns of the iterated table (or the expanded table using [[RELATED]]), then it is possible to filter the table through the filter context by using [[CALCULATE]]. Moreover, if the measure referenced in the second argument of [[IF]] is additive, then the [[SUMX]] iterator can be removed.

## Example 1

Because the *Sales Amount* measure is additive, remove the [[SUMX]] iterator and the [[IF]] function. Wrap the *Sales Amount* measure in a [[CALCULATE]] function and move the condition from the first argument of the [[IF]] function to a filter argument of the [[CALCULATE]] function, embedded in [[KEEPFILTERS]].

### Original code

```dax
SUMX (
    Date,
    IF ( 'Date'[DateKey] = 1, [Sales Amount] )
)
```

### Possible optimization

```dax
CALCULATE ( 
    [Sales Amount], 
    KEEPFILTERS ( 'Date'[DateKey] = 1 )
)
```

## Example 2

Because the *Sales Amount* measure is additive, remove the [[SUMX]] iterator and the [[IF]] and [[RELATED]] functions. Wrap the *Sales Amount* measure in a [[CALCULATE]] function and move the condition (without [[RELATED]]) from the first argument of the [[IF]] function to a filter argument of the [[CALCULATE]] function.

### Original code

```dax
SUMX (
    'Product',
    IF ( RELATED (  'Product Category'[Category] ) = "Audio", [Sales Amount] )
)
```

### Possible optimization

```dax
CALCULATE ( 
    [Sales Amount], 
    KEEPFILTERS ( 'Product Category'[Category] = "Audio" )
)
```

## Example 3

Because the *Last Customer Balance* measure is non-additive, remove the [[SUMX]] iterator and the [[IF]] and [[RELATED]] functions. Wrap the *Sales Amount* measure in a [[CALCULATE]] function and move the condition (without [[RELATED]]) from the first argument of the [[IF]] function to a filter argument of the [[CALCULATE]] function.

### Original code

```dax
SUMX (
    Customer,
    IF (  Customer[Country] = "Canada", [Last Customer Balance] )
)
```

### Possible optimization

```dax
CALCULATE ( 
    SUMX (
        Customer,
        [Last Customer Balance]
    ),
    KEEPFILTERS ( Customer[Country] = "Canada" )
)
```
