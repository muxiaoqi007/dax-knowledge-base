---
title: "RUNNINGSUM"
function: "runningsum"
category: "Visual calculations"
url: "https://dax.guide/runningsum/"
source: "dax.guide"
重要度:
难度:
---

# RUNNINGSUM DAX Function (Visual calculations)

Calculates a running sum along the specified axis of the Visual Calculation data grid.

## Syntax

RUNNINGSUM ( <Column> [, <Axis>] [, <OrderBy>] [, <Blanks>] [, <Reset>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Column |  | The column to be evaluated for each row. |
| Axis | Optional | An axis reference. |
| OrderBy | Optional | Columns that define how each partition is sorted. |
| Blanks | Optional | An enumeration that defines how [[BLANK]] values are ordered. Valid values are: DEFAULT, [[LAST]], [[FIRST]]. |
| Reset | Optional | Specifies how the calculation restarts. Valid values are: None, LowestParent, HighestParent, or an integer. |

## Notes

The visual calculation functions have an axis parameter that is defined in the [VISUAL SHAPE](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/) query syntax produced by the visual calculation feature in Power BI.

## Related articles

Learn more about RUNNINGSUM in the following articles:

- [**Computing open orders with visual calculations in DAX**](https://www.sqlbi.com/articles/computing-open-orders-with-visual-calculations-in-dax/)

  This article describes the use of visual calculations for a scenario where they may be particularly relevant: computing open orders at the end of a time period. [» Read more](https://www.sqlbi.com/articles/computing-open-orders-with-visual-calculations-in-dax/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/runningsum-function-dax](https://learn.microsoft.com/en-us/dax/runningsum-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
