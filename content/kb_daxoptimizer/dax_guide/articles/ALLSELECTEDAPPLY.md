---
title: "ALLSELECTEDAPPLY"
function: "allselectedapply"
category: "Filter"
url: "https://dax.guide/allselectedapply/"
source: "dax.guide"
重要度:
难度:
---

# ALLSELECTEDAPPLY DAX Function (Filter)

Modifies how filters are applied while evaluating a [[GROUPCROSSAPPLY]] or [[GROUPCROSSAPPLYTABLE]] function.

## Syntax

ALLSELECTEDAPPLY ( <TableExpression> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| TableExpression |  | Any table expression. |

## Return values

Table An entire table or a table with one or more columns.

## Remarks

This function is mainly used internally by Power BI to query remote models in composite models.

You use ALLSELECTEDAPPLY within the context [[GROUPCROSSAPPLY]] and [[GROUPCROSSAPPLYTABLE]] functions, to override the standard behavior of those functions.

When a filter is specified as ALLSELECTEDAPPLY, it is initially hidden in the filter context. Upon an [[ALLSELECTED]] inside [[CALCULATE]] or [[CALCULATETABLE]], this table expression is enabled in fliter context.

## Related functions

Other related functions are:

- [[ALLSELECTED]]
- [[ALLSELECTEDREMOVE]]
- [[GROUPCROSSAPPLY]]
- [[GROUPCROSSAPPLYTABLE]]

Last update: Dec 28, 2025   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/allselectedapply-function-dax](https://learn.microsoft.com/en-us/dax/allselectedapply-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
