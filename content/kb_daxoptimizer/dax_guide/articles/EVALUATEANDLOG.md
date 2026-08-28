---
title: "EVALUATEANDLOG"
function: "evaluateandlog"
category: "Information"
url: "https://dax.guide/evaluateandlog/"
source: "dax.guide"
重要度:
难度:
---

# EVALUATEANDLOG DAX Function (Information)

Return the value of the first argument and also log the value in DAX evaluation log.

## Syntax

EVALUATEANDLOG ( <Value> [, <Label>] [, <MaxRows>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Value |  | A scalar or table expression. |
| Label | Optional | When specified, it is included in the Label column of the DAX Evaluation Log event as well as a part of the JSON payload. |
| MaxRows | Optional | Specify the number of rows to return in case of a table expression. The default value is 10. |

## Return values

Scalar A single [variant](https://dax.guide/dt/variant/) value.

The result of the evaluated Expression.

## Remarks

The DAX Evaluation Log is a trace event that can be captured by SQL Server Profiler or by the [DAX Debug Output](https://github.com/pbidax/DAXDebugOutput) tool.

## Related articles

Learn more about EVALUATEANDLOG in the following articles:

- [**Introducing the DAX EvaluateAndLog function**](https://pbidax.wordpress.com/2022/08/16/introduce-the-dax-evaluateandlog-function/)

  Describe how to use the DAX EvaluateAndLog function. [» Read more](https://pbidax.wordpress.com/2022/08/16/introduce-the-dax-evaluateandlog-function/)
- [**Debugging DAX measures in Power BI**](https://www.sqlbi.com/articles/debugging-dax-measures-in-power-bi/)

  This article describes different techniques to debug a DAX measure that returns an incorrect result, with and without external tools. [» Read more](https://www.sqlbi.com/articles/debugging-dax-measures-in-power-bi/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/dax/evaluateandlog-function-dax](https://learn.microsoft.com/dax/evaluateandlog-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
