---
title: "ACCRINT"
function: "accrint"
category: "Financial"
url: "https://dax.guide/accrint/"
source: "dax.guide"
重要度:
难度:
---

# ACCRINT DAX Function (Financial)

Returns the accrued interest for a security that pays periodic interest.

## Syntax

ACCRINT ( <Issue>, <First\_interest>, <Settlement>, <Rate>, <Par>, <Frequency> [, <Basis>] [, <Calc\_method>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Issue |  | The security’s issue date. |
| First\_interest |  | The security’s first interest date. |
| Settlement |  | The security’s settlement date. The security settlement date is the date after the issue date when the security is traded to the buyer. |
| Rate |  | The security’s annual coupon rate. |
| Par |  | The security’s par value. |
| Frequency |  | The number of coupon payments per year. For annual payments, frequency = 1; for semiannual, frequency = 2; for quarterly, frequency = 4. |
| Basis | Optional | The type of day count basis to use. |
| Calc\_method | Optional | A logical value that specifies the way to calculate the total accrued interest when the date of settlement is later than the date of first\_interest. A value of TRUE (1) returns the total accrued interest from issue to settlement. A value of FALSE (0) returns the accrued interest from first\_interest to settlement. If you do not enter the argument, it defaults to TRUE. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/accrint-function-dax](https://docs.microsoft.com/en-us/dax/accrint-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
