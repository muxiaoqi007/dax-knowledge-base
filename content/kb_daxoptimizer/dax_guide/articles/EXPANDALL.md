---
title: "EXPANDALL"
function: "expandall"
category: "Visual calculations"
url: "https://dax.guide/expandall/"
source: "dax.guide"
重要度:
难度:
---

# EXPANDALL DAX Function (Visual calculations)

Retrieves a context with added detail levels along an axis compared to the current context. With an expression, returns its value in the new context, enabling navigation to the lowest level on the axis, and is the inverse of [[COLLAPSEALL]].

## Syntax

EXPANDALL ( [<Expression>], <Axis> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Expression | Optional | The expression to be evaluated in the new context. |
| Axis |  | An axis reference. |

## Notes

The visual calculation functions have an axis parameter that is defined in the [VISUAL SHAPE](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/) query syntax produced by the visual calculation feature in Power BI.

## Related articles

Learn more about EXPANDALL in the following articles:

- [**Introducing EXPAND and COLLAPSE for visual calculations in Power BI**](https://www.sqlbi.com/articles/introducing-expand-and-collapse-for-visual-calculations/)

  This article introduces the two basic visual context navigation functions: EXPAND and COLLAPSE. [» Read more](https://www.sqlbi.com/articles/introducing-expand-and-collapse-for-visual-calculations/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/expandall-function-dax](https://learn.microsoft.com/en-us/dax/expandall-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
