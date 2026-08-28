---
title: "PPMT"
function: "ppmt"
category: "Financial"
url: "https://dax.guide/ppmt/"
source: "dax.guide"
重要度:
难度:
---

# PPMT DAX Function (Financial)

Returns the payment on the principal for a given period for an investment based on periodic, constant payments and a constant interest rate.

## Syntax

PPMT ( <Rate>, <Per>, <Nper>, <Pv> [, <Fv>] [, <Type>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Rate |  | The interest rate for the loan. |
| Per |  | Specifies the period and must be in the range 1 to nper. |
| Nper |  | The total number of payments for the loan. |
| Pv |  | The present value, or the total amount that a series of future payments is worth now; also known as the principal. |
| Fv | Optional | The future value, or a cash balance you want to attain after the last payment is made. If fv is omitted, it is assumed to be [[BLANK]]. |
| Type | Optional | The number 0 (zero) or 1 and indicates when payments are due. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/ppmt-function-dax](https://docs.microsoft.com/en-us/dax/ppmt-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
