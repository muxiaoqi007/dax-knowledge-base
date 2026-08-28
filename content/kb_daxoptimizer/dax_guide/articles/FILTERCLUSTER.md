---
title: "FILTERCLUSTER"
function: "filtercluster"
category: "Filter"
url: "https://dax.guide/filtercluster/"
source: "dax.guide"
重要度:
难度:
---

# FILTERCLUSTER DAX Function (Filter)

Returns a correlated join table over a set of groups.

## Syntax

FILTERCLUSTER( <GroupBy\_ColumnName> [, <GroupBy\_ColumnName> [, … ] ], <FilterTable> [, <FilterTable> [, … ] ], <Separator>, <TableScan> [, <TableScan> [, … ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| GroupBy\_ColumnName | Repeatable | A fully qualified column reference (Table[Column]) to a base table for which the distinct values are included in the returned table. Each GroupBy\_ColumnName column is cross-joined (different tables) or auto-existed (same table) with the subsequent specified columns. |
| FilterTable | Repeatable | A table expression participating in the join. |
| Separator |  | A string literal which serves no purpose other than separating FilterTable parameter with TableScan parameter. |
| TableScan | Repeatable | A table scan that joins with FilterTable parameters, applying autoexist semantics, and returns columns specified in GroupBy\_ColumnName. |

## Return values

Table An entire table or a table with one or more columns.

A table which includes combinations of values from the supplied columns based on the grouping specified. The column only includes column specified by GroupBy\_ColumnName parameter.

## Remarks

This function is mainly used internally by Power BI to query remote models in composite models.

FILTERCLUSTER function can only be used inside [[GROUPCROSSAPPLY]] and [[GROUPCROSSAPPLYTABLE]] functions.

FILTERCLUSTER is semantically equivalent to a natural join across all FilterTable and TableScan parameters, and then group by columns specified by GroupBy\_ColumnName parameters. Group by columns must come from TableScan parameters.

TableScan parameters are evaluated in the context of FilterTable.

## Related functions

Other related functions are:

- [[GROUPCROSSAPPLY]]
- [[GROUPCROSSAPPLYTABLE]]

Last update: Dec 28, 2025   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/filtercluster-function-dax](https://learn.microsoft.com/en-us/dax/filtercluster-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
