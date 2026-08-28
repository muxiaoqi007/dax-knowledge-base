---
title: "COLLAPSE"
function: "collapse"
category: "Visual calculations"
url: "https://dax.guide/collapse/"
source: "dax.guide"
重要度:
难度:
---

# COLLAPSE DAX Function (Visual calculations)

Retrieves a context with removed detail levels compared to the current context. With an expression, returns its value in the new context, allowing navigation up hierarchies and calculation at a coarser level of detail.

## Syntax

COLLAPSE ( [<Expression>] [, <Axis>] [, <Column> [, <Column> [, … ] ] ] [, <N>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Expression | Optional | The expression to be evaluated in the new context. |
| Axis | Optional | An axis reference. |
| Column | Optional Repeatable | A column in the data grid. |
| N | Optional | The number of levels to collapse. If omitted, the default value is 1. |

## Notes

The visual calculation functions have an axis parameter that is defined in the [VISUAL SHAPE](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/) query syntax produced by the visual calculation feature in Power BI.

## Related articles

Learn more about COLLAPSE in the following articles:

- [**Introducing VISUAL SHAPE for visual calculations in Power BI**](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/)

  This article introduces the VISUAL SHAPE clause, which defines a hierarchical structure for a table used in visual calculations. [» Read more](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/)
- [**Introducing EXPAND and COLLAPSE for visual calculations in Power BI**](https://www.sqlbi.com/articles/introducing-expand-and-collapse-for-visual-calculations/)

  This article introduces the two basic visual context navigation functions: EXPAND and COLLAPSE. [» Read more](https://www.sqlbi.com/articles/introducing-expand-and-collapse-for-visual-calculations/)
- [**Using EXPAND and COLLAPSE in visual calculations**](https://www.sqlbi.com/articles/using-expand-and-collapse-in-visual-calculations/)

  This article provides examples of visual calculations where the use of EXPAND and COLLAPSE is required to obtain the correct result. [» Read more](https://www.sqlbi.com/articles/using-expand-and-collapse-in-visual-calculations/)
- [**Dynamic Pareto analysis in Power BI**](https://www.sqlbi.com/articles/dynamic-pareto-analysis-in-power-bi/)

  This article describes how to implement a dynamic Pareto calculation in Power BI based on a measure that can be selected from a slicer and dynamically filtered by other slicers in the report. [» Read more](https://www.sqlbi.com/articles/dynamic-pareto-analysis-in-power-bi/)
- [**Using visual calculations to highlight an entire row**](https://www.sqlbi.com/articles/using-visual-calculations-to-highlight-an-entire-row/)

  Visual calculations can be used efficiently to format visuals. This article presents an interesting technique to highlight a row based solely on the maximum value in the last column. [» Read more](https://www.sqlbi.com/articles/using-visual-calculations-to-highlight-an-entire-row/)
- [**Dynamic formatting by hierarchy level with ISINSCOPE and ISATLEVEL**](https://www.sqlbi.com/articles/dynamic-formatting-by-hierarchy-level-with-isinscope-and-isatlevel/)

  This article describes how to apply different formatting rules at each level of a hierarchy (one rule at the year level, another at the quarter level, another at the month level) using ISINSCOPE in a measure or ISATLEVEL in a visual calculation. [» Read more](https://www.sqlbi.com/articles/dynamic-formatting-by-hierarchy-level-with-isinscope-and-isatlevel/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/collapse-function-dax](https://learn.microsoft.com/en-us/dax/collapse-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
