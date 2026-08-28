---
title: "COUPNUM"
function: "coupnum"
category: "Financial"
url: "https://dax.guide/coupnum/"
source: "dax.guide"
重要度:
难度:
---

# COUPNUM DAX Function (Financial)

Returns the number of coupons payable between the settlement date and maturity date, rounded up to the nearest whole coupon.

## Syntax

COUPNUM ( <Settlement>, <Maturity>, <Frequency> [, <Basis>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Settlement |  | The security’s settlement date. The security settlement date is the date after the issue date when the security is traded to the buyer. |
| Maturity |  | The security’s maturity date. The maturity date is the date when the security expires. |
| Frequency |  | The number of coupon payments per year. For annual payments, frequency = 1; for semiannual, frequency = 2; for quarterly, frequency = 4. |
| Basis | Optional | The type of day count basis to use. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/coupnum-function-dax](https://docs.microsoft.com/en-us/dax/coupnum-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
