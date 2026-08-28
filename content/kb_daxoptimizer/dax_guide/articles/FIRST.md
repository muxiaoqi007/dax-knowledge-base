---
title: "FIRST"
function: "first"
category: "Visual calculations"
url: "https://dax.guide/first/"
source: "dax.guide"
重要度:
难度:
---

# FIRST DAX Function (Visual calculations)

The First function retrieves a value in the Visual Calculation data grid from the first row of an axis.

## Syntax

FIRST ( <Column> [, <Axis>] [, <OrderBy>] [, <Blanks>] [, <Reset>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Column |  | The column from which to retrieve the value. |
| Axis | Optional | An axis reference. |
| OrderBy | Optional | Columns that define how each partition is sorted. |
| Blanks | Optional | Defines how to handle [[BLANK]] OrderBy values. Valid values include: DEFAULT, FIRST, [[LAST]]. |
| Reset | Optional | Specifies how the calculation restarts. Valid values are: None, LowestParent, HighestParent, or an integer. |

## Notes

The visual calculation functions have an axis parameter that is defined in the [VISUAL SHAPE](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/) query syntax produced by the visual calculation feature in Power BI.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/first-function-dax](https://learn.microsoft.com/en-us/dax/first-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
