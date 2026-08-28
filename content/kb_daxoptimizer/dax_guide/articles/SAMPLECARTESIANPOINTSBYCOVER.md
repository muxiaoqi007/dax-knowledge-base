---
title: "SAMPLECARTESIANPOINTSBYCOVER"
function: "samplecartesianpointsbycover"
url: "https://dax.guide/samplecartesianpointsbycover/"
source: "dax.guide"
重要度:
难度:
---

# SAMPLECARTESIANPOINTSBYCOVER DAX Function

Returns a sample subset from a given table expression that is obtained by plotting the rows as points in a 2D space, based on the input coordinates and radius columns, and then removing overlapping points.

## Syntax

SAMPLECARTESIANPOINTSBYCOVER ( <Size>, <Table>, <XAxis>, <YAxis> [, <Radius>] [, <MaxMinRatio>] [, <MaxBlankRatio>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Size |  | Number of rows in the sample to be returned. |
| Table |  | A table expression from which the sample is generated. |
| XAxis |  | The numerical XAxis column from the specified table. |
| YAxis |  | The numerical YAxis column from the specified table. |
| Radius | Optional | The numerical Radius column from the specified table. |
| MaxMinRatio | Optional | When we have a radius column, what is the ratio between the maximum and the minimum drawn points. |
| MaxBlankRatio | Optional | When we have a radius column, what is the ratio between the maximum drawn point and the points that have blank radius value. |

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation not available.  
The function may be undocumented or unsupported. Check the Compatibility box on this page.

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
