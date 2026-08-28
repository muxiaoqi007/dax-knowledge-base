---
title: "SWITCH cannot be reduced statically due to missing arg pre-conditions (no [Value] args are constants)"
kb_id: "102201"
url: "https://kb.daxoptimizer.com/d/102201"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# SWITCH cannot be reduced statically due to missing arg pre-conditions (no [Value] args are constants)

The [[SWITCH]] function cannot be optimized by applying static branch reduction to the query plan. [Value] pre-conditions are partially met: none of the [Value] arguments are constants.

## Remarks

The [[SWITCH]] function produces a query plan for all the possible branches unless particular conditions are met. In the optimal conditions, the query plan only generates the query plan for the branches that could be executed. When this is not possible, there could be a formula engine cost to evaluate branches that are never executed for a given query.

Because none of the *Value* arguments of the [[SWITCH]] are constants, the static branch reduction is not possible.

To optimize the query plan, in its *Expression* argument [[SWITCH]] should test only one column that is directly filtered in the report, and the *Value* arguments should be constant values.

Read more about this optimization:

- [Understanding the optimization of SWITCH](https://www.sqlbi.com/articles/understanding-the-optimization-of-switch)

## Example

**Scenario**: The model has a *Metric* table with three columns: *Metric[Name]* is a visible text, *Metric[Language]* is a visible text that defines the localization of *Metric[Name]*, and *Metric[ID]* is a hidden column with a number that identifies the metric regardless of the language. The [[SWITCH]] functions tests the visible *Metric[Name]* column by comparing the value with the corresponding name retrieved using the currently selected language with the measures *MetricName Revenue*, *MetricName Cost*, and *MetricName Margin*.

```dax
MetricName Revenue :=
CALCULATE ( 
    SELECTEDVALUE ( Metric[Name] ),
    Metric[ID] = 1
 )
 
MetricName Cost :=
CALCULATE ( 
    SELECTEDVALUE ( Metric[Name] ),
    Metric[ID] = 2
)

MetricName Margin :=
CALCULATE ( 
    SELECTEDVALUE ( Metric[Name] ),
    Metric[ID] = 3
 )
```

**Action**: Expose *Metric[ID]* as a visible column for a direct selection, or use it in the Group By Column property of *Metric[Name]* as described in [Understanding Group By Columns in Power BI](https://www.sqlbi.com/articles/understanding-group-by-columns-in-power-bi/). Then, use *Metric[ID]* in [[SWITCH]] *Expression* argument and constant values in *Value* arguments.

### Original code

```dax
SWITCH (
    SELECTEDVALUE ( Metric[Name] ),
    [MetricName Revenue], [Sales Amount],
    [MetricName Cost], [Total Cost],
    [MetricName Margin], [Margin],
    BLANK()
)
```

### Possible optimization

Expose *Metric[ID]* as a visible column for a direct selection, or use it in the Group By Column property of *Metric[Name]* as described in [Understanding Group By Columns in Power BI](https://www.sqlbi.com/articles/understanding-group-by-columns-in-power-bi/). Then, use *Metric[ID]* in [[SWITCH]] *Expression* argument and constant values in *Value* arguments.

```dax
SWITCH (
    SELECTEDVALUE ( Metric[ID] ),
    1, [Sales Amount],
    2, [Total Cost],
    3, [Margin],
    BLANK()
)
```
