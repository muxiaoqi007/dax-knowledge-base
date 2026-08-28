---
title: "VDB"
function: "vdb"
category: "Financial"
url: "https://dax.guide/vdb/"
source: "dax.guide"
重要度:
难度:
---

# VDB DAX Function (Financial)

Returns the depreciation of an asset for any period you specify, including partial periods, using the double-declining balance method or some other method you specify. VDB stands for variable declining balance.

## Syntax

VDB ( <Cost>, <Salvage>, <Life>, <Start\_period>, <End\_period> [, <Factor>] [, <No\_switch>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Cost |  | The initial cost of the asset. |
| Salvage |  | The value at the end of the depreciation (sometimes called the salvage value of the asset). This value can be 0. |
| Life |  | The number of periods over which the asset is depreciated (sometimes called the useful life of the asset). |
| Start\_period |  | The starting period for which you want to calculate the depreciation. Start\_period must use the same units as life. |
| End\_period |  | The ending period for which you want to calculate the depreciation. End\_period must use the same units as life. |
| Factor | Optional | The rate at which the balance declines. If factor is omitted, it is assumed to be 2 (the double-declining balance method). |
| No\_switch | Optional | A logical value specifying whether to switch to straight-line depreciation when depreciation is greater than the declining balance calculation. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/vdb-function-dax](https://docs.microsoft.com/en-us/dax/vdb-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
