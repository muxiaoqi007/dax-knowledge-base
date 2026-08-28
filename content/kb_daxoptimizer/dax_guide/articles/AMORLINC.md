---
title: "AMORLINC"
function: "amorlinc"
category: "Financial"
url: "https://dax.guide/amorlinc/"
source: "dax.guide"
重要度:
难度:
---

# AMORLINC DAX Function (Financial)

Returns the depreciation for each accounting period. This function is provided for the French accounting system. If an asset is purchased in the middle of the accounting period, the prorated depreciation is taken into account.

## Syntax

AMORLINC ( <Cost>, <Date\_purchased>, <First\_period>, <Salvage>, <Period>, <Rate> [, <Basis>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Cost |  | The cost of the asset. |
| Date\_purchased |  | The date of the purchase of the asset. |
| First\_period |  | The date of the end of the first period. |
| Salvage |  | The salvage value at the end of the life of the asset. |
| Period |  | The period. |
| Rate |  | The rate of depreciation. |
| Basis | Optional | The year basis to be used. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/amorlinc-function-dax](https://docs.microsoft.com/en-us/dax/amorlinc-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
