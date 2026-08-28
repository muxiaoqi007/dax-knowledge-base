---
title: "ALWAYSAPPLY"
function: "alwaysapply"
category: "Filter"
url: "https://dax.guide/alwaysapply/"
source: "dax.guide"
重要度:
难度:
---

# ALWAYSAPPLY DAX Function (Filter)

Modifies how filters are applied while evaluating a [[GROUPCROSSAPPLY]] or [[GROUPCROSSAPPLYTABLE]] function.

## Syntax

ALWAYSAPPLY ( <TableExpression> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| TableExpression |  | Any table expression. |

## Return values

Table An entire table or a table with one or more columns.

## Remarks

This function is mainly used internally by Power BI to query remote models in composite models.

You use ALWAYSAPPLY within the context [[GROUPCROSSAPPLY]] and [[GROUPCROSSAPPLYTABLE]] functions, to override the standard behavior of those functions.

By default, a value filter does not affect measure if it doesn’t change filter context. This behavior can be controlled by ALWAYSAPPLY function so that an empty table can still affect measure even if it doesn’t change filter context.

## Related functions

Other related functions are:

- [[GROUPCROSSAPPLY]]
- [[GROUPCROSSAPPLYTABLE]]

Last update: Dec 28, 2025   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/alwaysapply-function-dax](https://learn.microsoft.com/en-us/dax/alwaysapply-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
