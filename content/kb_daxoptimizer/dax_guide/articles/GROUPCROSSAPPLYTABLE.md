---
title: "GROUPCROSSAPPLYTABLE"
function: "groupcrossapplytable"
category: "Filter"
url: "https://dax.guide/groupcrossapplytable/"
source: "dax.guide"
重要度:
难度:
---

# GROUPCROSSAPPLYTABLE DAX Function (Filter)

Returns a summary table over a set of groups.

## Syntax

GROUPCROSSAPPLY ( <GroupBy\_ColumnName> [, <GroupBy\_ColumnName> [, … ] ], <FilterTable> [, <FilterTable> [, … ] ] [, <Name>, <TableExpression> [, … ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| GroupBy\_ColumnName | Repeatable | A fully qualified column reference (Table[Column]) to a base table for which the distinct values are included in the returned table. |
| FilterTable | Repeatable | A table expression which is added to the filter context of all columns specified as GroupBy\_ColumnName arguments. |
| Name | Repeatable | A string literal which serves no purpose other than separating FilterTable parameter with TableExpression parameter. |
| TableExpression | Repeatable | A table expression that is evaluated under filter context of filterTable parameters and returned as a part of the join in the return value |

## Return values

Table An entire table or a table with one or more columns.

A table which includes combinations of values from the supplied columns based on the grouping specified.

## Remarks

This function is mainly used internally by Power BI to query remote models in composite models.

GROUPCROSSAPPLYTABLE is similar to GROUPCROSSJOIN function. All FilterTable parameters are cross-join. [[FILTERCLUSTER]] function can be used to perform natural joins of filter tables or group by columns if needed.

The table expression parameter is evaluated in the filter context containing all FilterTable parameters.

You can modify filtering behavior of FilterTable by using the following functions: [[ALLSELECTEDAPPLY]], [[ALLSELECTEDREMOVE]], [[ALWAYSAPPLY]], [[KEEPFILTERS]], [[SHADOWCLUSTER]], [[NONFILTER]].

## Related articles

Learn more about GROUPCROSSAPPLYTABLE in the following articles:

- [**Using ALLSELECTED in composite models**](https://www.sqlbi.com/articles/using-allselected-in-composite-models/)

  Using ALLSELECTED with no arguments in a remote model later used in a composite model might produce unexpected results. In this article we examine the topic and provide the reasons why ALLSELECTED requires special attention. [» Read more](https://www.sqlbi.com/articles/using-allselected-in-composite-models/)

## Related functions

Other related functions are:

- [[ALLSELECTEDAPPLY]]
- [[ALLSELECTEDREMOVE]]
- [[ALWAYSAPPLY]]
- [[FILTERCLUSTER]]
- [[KEEPFILTERS]]
- [[NONFILTER]]
- [[SHADOWCLUSTER]]

Last update: Dec 28, 2025   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/groupcrossapplytable-function-dax](https://learn.microsoft.com/en-us/dax/groupcrossapplytable-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
