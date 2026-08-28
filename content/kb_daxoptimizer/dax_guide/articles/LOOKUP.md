---
title: "LOOKUP"
function: "lookup"
category: "Filter"
url: "https://dax.guide/lookup/"
source: "dax.guide"
重要度:
难度:
---

# LOOKUP DAX Function (Filter)

Identifies cells where specified columns match evaluated expressions, and retrieves a column value (only when a single cell is identified) or calculates an expression’s value from those matching cells. Filter context from columns not specified will be applied implicitly.

## Syntax

LOOKUP ( <Result\_ColumnOrExpression>, <Search\_Column>, <Search\_Value> [, <Search\_Column>, <Search\_Value> [, … ] ] [, <AssociatedColumnsBehavior>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Result\_ColumnOrExpression |  | Specifies the column to retrieve a value from or an expression to evaluate over the identified cells where search\_columns match search\_values. |
| Search\_Column | Repeatable | The column that contains search\_value. |
| Search\_Value | Repeatable | The value that you want to find in search\_column. |
| AssociatedColumnsBehavior | Optional | Controls how filter context is cleared for columns associated with the search columns. Valid values are: Explicit (default) – the author must supply a value for every relevant column; Inferred – the engine clears filter context for group-by and sort-by columns associated with the search columns via the visual shape. |

## Return values

Scalar A single value of any type.

The value of the column found, or the result of the expression evaluated in the matching cells.

## Related functions

Other related functions are:

- [[LOOKUPWITHTOTALS]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/lookup-function-dax](https://learn.microsoft.com/en-us/dax/lookup-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
