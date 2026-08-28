---
title: "RATE"
function: "rate"
category: "Financial"
url: "https://dax.guide/rate/"
source: "dax.guide"
重要度:
难度:
---

# RATE DAX Function (Financial)

Returns the interest rate per period of an annuity. RATE is calculated by iteration and can have zero or more solutions. If the successive results of RATE do not converge to within 0.0000001 after 20 iterations, RATE returns an error.

## Syntax

RATE ( <Nper>, <Pmt>, <Pv> [, <Fv>] [, <Type>] [, <Guess>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Nper |  | The total number of payment periods in an annuity. |
| Pmt |  | The payment made each period, which cannot change over the life of the annuity. Typically, pmt includes principal and interest but no other fees or taxes. |
| Pv |  | The present value, or the total amount that a series of future payments is worth now. |
| Fv | Optional | The future value, or a cash balance you want to attain after the last payment is made. If fv is omitted, it is assumed to be 0 (the future value of a loan, for example, is 0). |
| Type | Optional | The number 0 or 1, which indicates when payments are due. |
| Guess | Optional | Your guess for what the rate will be. If guess is omitted, it is assumed to be 10%. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/rate-function-dax](https://docs.microsoft.com/en-us/dax/rate-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
