---
title: "INFO.VIEW.RELATIONSHIPS"
function: "info-view-relationships"
category: "Information"
url: "https://dax.guide/info-view-relationships/"
source: "dax.guide"
重要度:
难度:
---

# INFO.VIEW.RELATIONSHIPS DAX Function (Information)

Returns a list of all relationships in the current model.

## Syntax

INFO.VIEW.RELATIONSHIPS ( )

This expression has no parameters.

## Return values

| Field | Type |
| --- | --- |
| ID | Integer || Name | String || Relationship | String || Model | String || IsActive | Boolean || CrossFilteringBehavior | String || RelyOnReferentialIntegrity | Boolean || FromTable | String || FromColumn | String || FromCardinality | String || ToTable | String || ToColumn | String || ToCardinality | String || State | String || SecurityFilteringBehavior | String |

## Remarks

View on top of the TMSCHEMA\_RELATIONSHIPS data management view (DMV), which remove the need of joins with other tables to decode internal IDs.

As other INFO.VIEW functions, it can be used in measures, calculated columns, and calculated tables (a restriction exists for the INFO functions, but it does not apply to INFO.VIEW).

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Daniil Maslyuk

Microsoft documentation not available.  
The function may be undocumented or unsupported. Check the Compatibility box on this page.

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
