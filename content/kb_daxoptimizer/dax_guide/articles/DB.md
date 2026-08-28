---
title: "DB"
function: "db"
category: "Financial"
url: "https://dax.guide/db/"
source: "dax.guide"
重要度:
难度:
---

# DB DAX Function (Financial)

Returns the depreciation of an asset for a specified period using the fixed-declining balance method.

## Syntax

DB ( <Cost>, <Salvage>, <Life>, <Period> [, <Month>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Cost |  | The initial cost of the asset. |
| Salvage |  | The value at the end of the depreciation (sometimes called the salvage value of the asset). |
| Life |  | The number of periods over which the asset is being depreciated (sometimes called the useful life of the asset). |
| Period |  | The period for which you want to calculate the depreciation. Period must use the same units as life. |
| Month | Optional | The number of months in the first year. If omitted, it is assumed to be 12. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/db-function-dax](https://docs.microsoft.com/en-us/dax/db-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
