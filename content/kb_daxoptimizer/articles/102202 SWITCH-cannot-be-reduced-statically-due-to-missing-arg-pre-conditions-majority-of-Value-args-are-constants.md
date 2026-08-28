---
title: "SWITCH cannot be reduced statically due to missing arg pre-conditions (majority of [Value] args are constants)"
kb_id: "102202"
url: "https://kb.daxoptimizer.com/d/102202"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# SWITCH cannot be reduced statically due to missing arg pre-conditions (majority of [Value] args are constants)

The [[SWITCH]] function cannot be optimized by applying static branch reduction to the query plan. [Value] pre-conditions are partially met: the majority of the [Value] arguments are constants.

## Remarks

The [[SWITCH]] function produces a query plan for all the possible branches unless particular conditions are met. In the optimal conditions, the query plan only generates the query plan for the branches that could be executed. When this is not possible, there could be a formula engine cost to evaluate branches that are never executed for a given query.

Because the majority of the *Value* arguments of the [[SWITCH]] are constants and the *Expression* argument reads the value of a single physical column, separating those branches in a dedicated SWITCH can improve the query plan for those cases.

Read more about this optimization:

- [Understanding the optimization of SWITCH](https://www.sqlbi.com/articles/understanding-the-optimization-of-switch)

## Example

Move the test with the *KPI Metric* measure in a separate SWITCH in the Other condition, so the outer SWITCH has only constants in the *Value* arguments.

### Original code

```dax
SWITCH (
    SELECTEDVALUE ( Metric[Name] ),
    "Revenue", [Sales Amount],
    "Cost", [Total Cost],
    "Margin", [Margin],
    [KPI Metric], [KPI Value],
    BLANK()
)
```

### Possible optimization

```dax
SWITCH (
    SELECTEDVALUE ( Metric[Name]),
    "Revenue", [Sales Amount],
    "Cost", [Total Cost],
    "Margin", [Margin],
    SWITCH (
        SELECTEDVALUE ( Metric[Name] ),
        [KPI Metric], [KPI Value],
        BLANK()
    )
)
```
