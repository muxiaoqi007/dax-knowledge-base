---
title: "ALLSELECTEDREMOVE"
function: "allselectedremove"
category: "Filter"
url: "https://dax.guide/allselectedremove/"
source: "dax.guide"
重要度:
难度:
---

# ALLSELECTEDREMOVE DAX Function (Filter)

Modifies how filters are applied while evaluating a [[GROUPCROSSAPPLY]] or [[GROUPCROSSAPPLYTABLE]] function.

## Syntax

ALLSELECTEDREMOVE ( <TableExpression> [, <Column> [, <Column> [, …] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| TableExpression |  | Any table expression. |
| Column | Optional Repeatable | Column in the table expression. |

## Return values

Table An entire table or a table with one or more columns.

## Remarks

This function is mainly used internally by Power BI to query remote models in composite models.

You use ALLSELECTEDREMOVE within the context [[GROUPCROSSAPPLY]] and [[GROUPCROSSAPPLYTABLE]] functions, to override the standard behavior of those functions.

When doing [[ALLSELECTED]] on a column that appears on the list of ALLSELECTEDREMOVE column parameter, then this column from this table expression will not participate in the filter context. The same column from other table expression in the filter context is not changed.

It is possible to specify a subset of columns of the table expression. When no column is supplied, then it’s equivalent to supplying all columns of the table expression to ALLSELECTEDREMOVE.

## Related functions

Other related functions are:

- [[GROUPCROSSAPPLY]]
- [[GROUPCROSSAPPLYTABLE]]

Last update: Dec 28, 2025   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/allselectedremove-function-dax](https://learn.microsoft.com/en-us/dax/allselectedremove-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
