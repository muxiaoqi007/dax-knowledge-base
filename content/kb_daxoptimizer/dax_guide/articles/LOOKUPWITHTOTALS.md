---
title: "LOOKUPWITHTOTALS"
function: "lookupwithtotals"
category: "Filter"
url: "https://dax.guide/lookupwithtotals/"
source: "dax.guide"
重要度:
难度:
---

# LOOKUPWITHTOTALS DAX Function (Filter)

Identifies cells where specified columns match evaluated expressions, and retrieves a column value (only when a single cell is identified) or calculates an expression’s value from those matching cells. LOOKUPWITHTOTALS defaults the non-specified columns to the total, whereas [[LOOKUP]] does not change the filter over non-specified columns.

## Syntax

LOOKUPWITHTOTALS ( <Result\_ColumnOrExpression>, <Search\_Column>, <Search\_Value> [, <Search\_Column>, <Search\_Value> [, … ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Result\_ColumnOrExpression |  | Specifies the column to retrieve a value from or an expression to evaluate over the identified cells where search\_columns match search\_values. |
| Search\_Column | Repeatable | The column that contains search\_value. |
| Search\_Value | Repeatable | The value that you want to find in search\_column. |

## Return values

Scalar A single value of any type.

The value of the column found, or the result of the expression evaluated in the matching cells.

## Remarks

LOOKUPWITHTOTALS can be expressed by using [[COLLAPSE]]. Indeed, the two following formulas are equivalent:

```dax


LOOKUP 2018 Totals = COLLAPSEALL ( LOOKUP ( [Sales Amount], [Year], 2018 ) )



LOOKUP 2018 Totals = LOOKUPWITHTOTALS ( [Sales Amount], [Year], 2018 )

```

## Related functions

Other related functions are:

- [[LOOKUP]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/lookupwithtotals-function-dax](https://learn.microsoft.com/en-us/dax/lookupwithtotals-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
