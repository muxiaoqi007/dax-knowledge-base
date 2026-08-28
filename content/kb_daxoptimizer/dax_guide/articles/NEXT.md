---
title: "NEXT"
function: "next"
category: "Visual calculations"
url: "https://dax.guide/next/"
source: "dax.guide"
重要度:
难度:
---

# NEXT DAX Function (Visual calculations)

The Next function retrieves a value in the next row of an axis in the Visual Calculation data grid.

## Syntax

NEXT ( <Column> [, <Steps>] [, <Axis>] [, <OrderBy>] [, <Blanks>] [, <Reset>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Column |  | The column from which to retrieve the value. |
| Steps | Optional | Step is the offset value. The value should be greater than or equal to 1. Default is 1. |
| Axis | Optional | An axis reference. |
| OrderBy | Optional | Columns that define how each partition is sorted. |
| Blanks | Optional | Defines how to handle [[BLANK]] OrderBy values. Valid values include: DEFAULT, [[FIRST]], [[LAST]]. |
| Reset | Optional | Specifies how the calculation restarts. Valid values are: None, LowestParent, HighestParent, or an integer. |

## Notes

The visual calculation functions have an axis parameter that is defined in the [VISUAL SHAPE](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/) query syntax produced by the visual calculation feature in Power BI.

## Related articles

Learn more about NEXT in the following articles:

- [**Introducing VISUAL SHAPE for visual calculations in Power BI**](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/)

  This article introduces the VISUAL SHAPE clause, which defines a hierarchical structure for a table used in visual calculations. [» Read more](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/next-function-dax](https://learn.microsoft.com/en-us/dax/next-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
