---
title: "XNPV"
function: "xnpv"
category: "Financial"
url: "https://dax.guide/xnpv/"
source: "dax.guide"
重要度:
难度:
---

# XNPV DAX Function (Financial)

Returns the net present value for a schedule of cash flows.

## Syntax

XNPV ( <Table>, <Values>, <Dates>, <Rate> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | The table containing the rows for which the Values and Dates expressions will be evaluated. || Values  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | An expression to be evaluated for each row of the table, which will yield a series of cash flows. |
| Dates  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | An expression to be evaluated for each row of the table, which will yield a schedule of payment dates. |
| Rate |  | The discount rate to apply to the cash flows. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Net present value.

## Related functions

Other related functions are:

- [[XNPV]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/xnpv-function-dax](https://docs.microsoft.com/en-us/dax/xnpv-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
