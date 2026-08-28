---
title: "EXPAND"
function: "expand"
category: "Visual calculations"
url: "https://dax.guide/expand/"
source: "dax.guide"
重要度:
难度:
---

# EXPAND DAX Function (Visual calculations)

Retrieves a context with added levels of detail compared to the current context. If an expression is provided, returns its value in the new context, allowing for navigation in hierarchies and calculation at a more detailed level.

## Syntax

EXPAND ( [<Expression>] [, <Axis>] [, <Column> [, <Column> [, … ] ] ] [, <N>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Expression | Optional | The expression to be evaluated in the new context. |
| Axis | Optional | An axis reference. |
| Column | Optional Repeatable | A column in the data grid. |
| N | Optional | The number of levels to expand. If omitted, the default value is 1. |

## Notes

The visual calculation functions have an axis parameter that is defined in the [VISUAL SHAPE](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/) query syntax produced by the visual calculation feature in Power BI.

## Related articles

Learn more about EXPAND in the following articles:

- [**Introducing VISUAL SHAPE for visual calculations in Power BI**](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/)

  This article introduces the VISUAL SHAPE clause, which defines a hierarchical structure for a table used in visual calculations. [» Read more](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/)
- [**Introducing EXPAND and COLLAPSE for visual calculations in Power BI**](https://www.sqlbi.com/articles/introducing-expand-and-collapse-for-visual-calculations/)

  This article introduces the two basic visual context navigation functions: EXPAND and COLLAPSE. [» Read more](https://www.sqlbi.com/articles/introducing-expand-and-collapse-for-visual-calculations/)
- [**Using EXPAND and COLLAPSE in visual calculations**](https://www.sqlbi.com/articles/using-expand-and-collapse-in-visual-calculations/)

  This article provides examples of visual calculations where the use of EXPAND and COLLAPSE is required to obtain the correct result. [» Read more](https://www.sqlbi.com/articles/using-expand-and-collapse-in-visual-calculations/)
- [**SQLBI+ updates in April 2026**](https://www.sqlbi.com/blog/marco/2026/04/06/sqlbi-updates-in-april-2026/)

  We released a new session in SQLBI+: How to navigate the lattice of visual calculations: This video explains how to navigate the lattice of visual calculations in DAX, using COLLAPSEALL and EXPAND to move between levels. A key topic is the difference between ROWS/COLUMNS and VALUES of ROWS/COLUMNS. The video also shows how to create reusable visual calculation functions that encapsulate lattice navigation logic, simplifying otherwise complex code. Stay tuned for new SQLBI+ content coming in 2026! [» Read more](https://www.sqlbi.com/blog/marco/2026/04/06/sqlbi-updates-in-april-2026/)
- [**Using visual calculations to highlight an entire row**](https://www.sqlbi.com/articles/using-visual-calculations-to-highlight-an-entire-row/)

  Visual calculations can be used efficiently to format visuals. This article presents an interesting technique to highlight a row based solely on the maximum value in the last column. [» Read more](https://www.sqlbi.com/articles/using-visual-calculations-to-highlight-an-entire-row/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/expand-function-dax](https://learn.microsoft.com/en-us/dax/expand-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
