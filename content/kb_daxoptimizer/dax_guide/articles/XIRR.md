---
title: "XIRR"
function: "xirr"
category: "Financial"
url: "https://dax.guide/xirr/"
source: "dax.guide"
重要度:
难度:
---

# XIRR DAX Function (Financial)

Returns the internal rate of return for a schedule of cash flows that is not necessarily periodic.

## Syntax

XIRR ( <Table>, <Values>, <Dates> [, <Guess>] [, <AlternateResult>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | The table containing the rows for which the Values and Dates expressions will be evaluated. || Values  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | An expression to be evaluated for each row of the table, which will yield a series of cash flows. |
| Dates  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | An expression to be evaluated for each row of the table, which will yield a schedule of payment dates. |
| Guess | Optional | Optional. A number that you guess is close to the result of XIRR. |
| AlternateResult | Optional | Optional. The alternate result to return when XIRR cannot find a solution. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Internal rate of return for the given inputs. If the calculation fails to return a valid result, an error is returned.

## Related functions

Other related functions are:

- [[XNPV]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/xirr-function-dax](https://docs.microsoft.com/en-us/dax/xirr-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
