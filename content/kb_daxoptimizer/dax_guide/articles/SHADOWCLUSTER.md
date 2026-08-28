---
title: "SHADOWCLUSTER"
function: "shadowcluster"
category: "Filter"
url: "https://dax.guide/shadowcluster/"
source: "dax.guide"
重要度:
难度:
---

# SHADOWCLUSTER DAX Function (Filter)

Modifies how filters are applied while evaluating a [[GROUPCROSSAPPLY]] or [[GROUPCROSSAPPLYTABLE]] function.

## Syntax

SHADOWCLUSTER ( <TableExpression> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| TableExpression |  | Any table expression. |

## Return values

Table An entire table or a table with one or more columns.

## Remarks

This function is mainly used internally by Power BI to query remote models in composite models.

You use SHADOWCLUSTER within the context [[GROUPCROSSAPPLY]] and [[GROUPCROSSAPPLYTABLE]] functions, to override the standard behavior of those functions.

When a table is marked as SHADOWCLUSTER, it is marked internally as a “shadow” table expression so that it is only enabled in an [[ALLSELECTED]] context.

## Related functions

Other related functions are:

- [[GROUPCROSSAPPLY]]
- [[GROUPCROSSAPPLYTABLE]]

Last update: Dec 28, 2025   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/shadowcluster-function-dax](https://learn.microsoft.com/en-us/dax/shadowcluster-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
