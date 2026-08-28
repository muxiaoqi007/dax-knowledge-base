---
title: "INFO.VIEW.MEASURES"
function: "info-view-measures"
category: "Information"
url: "https://dax.guide/info-view-measures/"
source: "dax.guide"
重要度:
难度:
---

# INFO.VIEW.MEASURES DAX Function (Information)

Returns a list of all measures in the current model.

## Syntax

INFO.VIEW.MEASURES ( )

This expression has no parameters.

## Return values

| Field | Type |
| --- | --- |
| ID | Integer || Name | String || Table | String || Description | String || DataType | String || Expression | String || FormatString | String || IsHidden | Boolean || State | String || KPIID | Integer || IsSimpleMeasure | Boolean || DisplayFolder | String || DetailRowsDefinition | String || DataCategory | String || FormatStringDefinition | String || LineageTag | String |

## Examples

View on top of the TMSCHEMA\_MEASURES data management view (DMV), which remove the need of joins with other tables to decode internal IDs.

As other INFO.VIEW functions, it can be used in measures, calculated columns, and calculated tables (a restriction exists for the INFO functions, but it does not apply to INFO.VIEW).

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Daniil Maslyuk

Microsoft documentation not available.  
The function may be undocumented or unsupported. Check the Compatibility box on this page.

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
