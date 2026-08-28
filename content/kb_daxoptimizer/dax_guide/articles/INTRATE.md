---
title: "INTRATE"
function: "intrate"
category: "Financial"
url: "https://dax.guide/intrate/"
source: "dax.guide"
重要度:
难度:
---

# INTRATE DAX Function (Financial)

Returns the interest rate for a fully invested security.

## Syntax

INTRATE ( <Settlement>, <Maturity>, <Investment>, <Redemption> [, <Basis>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Settlement |  | The security’s settlement date. The security settlement date is the date after the issue date when the security is traded to the buyer. |
| Maturity |  | The security’s maturity date. The maturity date is the date when the security expires. |
| Investment |  | The amount invested in the security. |
| Redemption |  | The amount to be received at maturity. |
| Basis | Optional | The type of day count basis to use. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/intrate-function-dax](https://docs.microsoft.com/en-us/dax/intrate-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
