---
title: "RANGE"
function: "range"
category: "Visual calculations"
url: "https://dax.guide/range/"
source: "dax.guide"
重要度:
难度:
---

# RANGE DAX Function (Visual calculations)

Retrieves a range of rows within the specified axis, relative to the current row.

## Syntax

RANGE ( <Step> [, <IncludeCurrent>] [, <Axis>] [, <OrderBy>] [, <Blanks>] [, <Reset>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Step |  | The desired length of the window. If negative, the window will contain the last -STEP rows before the current row. Otherwise, the window will contain the first STEP rows after the current row. |
| IncludeCurrent | Optional | A logical value specifying whether or not to include the current row. Default value is TRUE. |
| Axis | Optional | An axis reference. |
| OrderBy | Optional | Columns that define how each partition is sorted. |
| Blanks | Optional | An enumeration that defines how [[BLANK]] values are ordered. Valid values are: DEFAULT, [[LAST]], [[FIRST]]. |
| Reset | Optional | Specifies how the calculation restarts. Valid values are: None, LowestParent, HighestParent, or an integer. |

## Notes

The visual calculation functions have an axis parameter that is defined in the [VISUAL SHAPE](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/) query syntax produced by the visual calculation feature in Power BI.

## Related articles

Learn more about RANGE in the following articles:

- [**Using REMOVEFILTERS in DAX user-defined functions**](https://www.sqlbi.com/articles/using-removefilters-in-dax-user-defined-functions/)

  In this article, we implement a function that removes filter-keep column filters from a calendar, using REMOVEFILTERS as the return value of the function. [» Read more](https://www.sqlbi.com/articles/using-removefilters-in-dax-user-defined-functions/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/range-function-dax](https://learn.microsoft.com/en-us/dax/range-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
