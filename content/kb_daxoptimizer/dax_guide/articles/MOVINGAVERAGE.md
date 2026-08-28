---
title: "MOVINGAVERAGE"
function: "movingaverage"
category: "Visual calculations"
url: "https://dax.guide/movingaverage/"
source: "dax.guide"
重要度:
难度:
---

# MOVINGAVERAGE DAX Function (Visual calculations)

Calculates a moving average along the specified axis of the Visual Calculation data grid.

## Syntax

MOVINGAVERAGE ( <Column>, <WindowSize> [, <IncludeCurrent>] [, <Axis>] [, <OrderBy>] [, <Blanks>] [, <Reset>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Column |  | The column to be evaluated for each row. |
| WindowSize |  | The length of the moving window, that is, how many rows will be included. This value should be greater than or equal to 1. |
| IncludeCurrent | Optional | A logical value specifying whether or not to include the current row. If FALSE, the window will contain the last WindowSize values before the current row. Default value is TRUE. |
| Axis | Optional | An axis reference. |
| OrderBy | Optional | Columns that define how each partition is sorted. |
| Blanks | Optional | An enumeration that defines how [[BLANK]] values are ordered. Valid values are: DEFAULT, [[LAST]], [[FIRST]]. |
| Reset | Optional | Specifies how the calculation restarts. Valid values are: None, LowestParent, HighestParent, or an integer. |

## Notes

The visual calculation functions have an axis parameter that is defined in the [VISUAL SHAPE](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/) query syntax produced by the visual calculation feature in Power BI.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/movingaverage-function-dax](https://learn.microsoft.com/en-us/dax/movingaverage-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
