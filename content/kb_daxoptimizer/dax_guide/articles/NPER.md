---
title: "NPER"
function: "nper"
category: "Financial"
url: "https://dax.guide/nper/"
source: "dax.guide"
重要度:
难度:
---

# NPER DAX Function (Financial)

Returns the number of periods for an investment based on periodic, constant payments and a constant interest rate.

## Syntax

NPER ( <Rate>, <Pmt>, <Pv> [, <Fv>] [, <Type>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Rate |  | The interest rate per period. |
| Pmt |  | The payment made each period; it cannot change over the life of the annuity. Typically, pmt contains principal and interest but no other fees or taxes. |
| Pv |  | The present value, or the lump-sum amount that a series of future payments is worth right now. |
| Fv | Optional | The future value, or a cash balance you want to attain after the last payment is made. If fv is omitted, it is assumed to be [[BLANK]]. |
| Type | Optional | The number 0 or 1 and indicates when payments are due. If type is omitted, it is assumed to be 0. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/nper-function-dax](https://docs.microsoft.com/en-us/dax/nper-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
