---
title: "ISPMT"
function: "ispmt"
category: "Financial"
url: "https://dax.guide/ispmt/"
source: "dax.guide"
重要度:
难度:
---

# ISPMT DAX Function (Financial)

Calculates the interest paid (or received) for the specified period of a loan (or investment) with even principal payments.

## Syntax

ISPMT ( <Rate>, <Per>, <Nper>, <Pv> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Rate |  | The interest rate for the investment. |
| Per |  | The period for which you want to find the interest, and must be between 1 and Nper. |
| Nper |  | The total number of payment periods for the investment. |
| Pv |  | The present value of the investment. For a loan, Pv is the loan amount. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/ispmt-function-dax](https://docs.microsoft.com/en-us/dax/ispmt-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
