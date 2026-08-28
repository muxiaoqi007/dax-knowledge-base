---
title: "SWITCH cannot be reduced statically"
kb_id: "102200"
url: "https://kb.daxoptimizer.com/d/102200"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# SWITCH cannot be reduced statically

The [[SWITCH]] function cannot be optimized by applying static branch reduction to the query plan. All the [Value] pre-conditions are met, but the prerequisites for optimal [[SWITCH]] column conditions are not satisfied.

## Remarks

The [[SWITCH]] function produces a query plan for all the possible branches unless particular conditions are met. In the optimal conditions, the query plan only generates the query plan for the branches that could be executed. When this is not possible, there could be a formula engine cost to evaluate branches that are never executed for a given query.

Even though all the *Value* arguments of the [[SWITCH]] are constants and the *Expression* argument reads the value of a single physical column, there is a dependency on another physical column that does not allow the static branch reduction of the query plan to obtain the full optimization.

To optimize the query plan, [[SWITCH]] should test the column that is directly filtered. For example, if a visible column is ordered by a hidden column, the [[SWITCH]] function should use the visible column and not the hidden column.

Read more about this optimization:

- [Understanding the optimization of SWITCH](https://www.sqlbi.com/articles/understanding-the-optimization-of-switch)

## Example

The model has a *Metric* table with two columns: *Metric[Name]* is a visible text and *Metric[ID]* is a hidden column with a number used to sort *Metric[Name]*. The user applies a filter to *Metric[Name]* through a slicer. The [[SWITCH]] *Expression* argument should get the value from *Metric[Name]* instead of *Metric[ID]* and test it with the corresponding text in the *Value* argument.

### Original code

```dax
SWITCH (
    SELECTEDVALUE ( Metric[ID] ),
    1, [Sales Amount],
    2, [Total Cost],
    3, [Margin],
    BLANK()
)
```

### Possible optimization

```dax
SWITCH (
    SELECTEDVALUE ( Metric[Name] ),
    "Revenue", [Sales Amount],
    "Cost", [Total Cost],
    "Margin", [Margin],
    BLANK()
)
```
