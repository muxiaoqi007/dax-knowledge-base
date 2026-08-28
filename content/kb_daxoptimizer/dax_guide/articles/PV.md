---
title: "PV"
function: "pv"
category: "Financial"
url: "https://dax.guide/pv/"
source: "dax.guide"
重要度:
难度:
---

# PV DAX Function (Financial)

calculates the present value of a loan or an investment, based on a constant interest rate. You can use PV with either periodic, constant payments (such as a mortgage or other loan), or a future value that’s your investment goal.

## Syntax

PV ( <Rate>, <Nper>, <Pmt> [, <Fv>] [, <Type>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Rate |  | The interest rate per period. |
| Nper |  | The total number of payment periods in an annuity. |
| Pmt |  | The payment made each period; it cannot change over the life of the annuity. Typically, pmt contains principal and interest but no other fees or taxes. |
| Fv | Optional | The future value, or a cash balance you want to attain after the last payment is made. If fv is omitted, it is assumed to be [[BLANK]]. |
| Type | Optional | The number 0 or 1 and indicates when payments are due. If type is omitted, it is assumed to be 0. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/pv-function-dax](https://docs.microsoft.com/en-us/dax/pv-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
