---
title: "ODDLYIELD"
function: "oddlyield"
category: "Financial"
url: "https://dax.guide/oddlyield/"
source: "dax.guide"
重要度:
难度:
---

# ODDLYIELD DAX Function (Financial)

Returns the yield of a security that has an odd (short or long) last period.

## Syntax

ODDLYIELD ( <Settlement>, <Maturity>, <Last\_interest>, <Rate>, <Pr>, <Redemption>, <Frequency> [, <Basis>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Settlement |  | The security’s settlement date. The security settlement date is the date after the issue date when the security is traded to the buyer. |
| Maturity |  | The security’s maturity date. The maturity date is the date when the security expires. |
| Last\_interest |  | The security’s last coupon date. |
| Rate |  | The security’s interest rate. |
| Pr |  | The security’s price. |
| Redemption |  | The security’s redemption value per $100 face value. |
| Frequency |  | The number of coupon payments per year. For annual payments, frequency = 1; for semiannual, frequency = 2; for quarterly, frequency = 4. |
| Basis | Optional | The type of day count basis to use. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/oddlyield-function-dax](https://docs.microsoft.com/en-us/dax/oddlyield-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
