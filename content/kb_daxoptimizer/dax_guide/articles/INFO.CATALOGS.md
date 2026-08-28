---
title: "INFO.CATALOGS"
function: "info-catalogs"
category: "Information"
url: "https://dax.guide/info-catalogs/"
source: "dax.guide"
重要度:
难度:
---

# INFO.CATALOGS DAX Function (Information)

Represents the DBSCHEMA\_CATALOGS DMV query function.

## Syntax

INFO.CATALOGS ( [<RestrictionName> [, [<RestrictionValue>] [, <RestrictionName> [, [<RestrictionValue>] [, … ] ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| RestrictionName | Optional Repeatable | Restriction name. |
| RestrictionValue | Optional Repeatable | Restriction value. |

## Return values

Table An entire table or a table with one or more columns.

| Field | Type |
| --- | --- |
| CATALOG\_NAME | String || DESCRIPTION | String || ROLES | String || DATE\_MODIFIED | DateTime || COMPATIBILITY\_LEVEL | Integer || TYPE | Integer || VERSION | Integer || DATABASE\_ID | String || DATABASE\_GUID | String || DATE\_QUERIED | DateTime || CURRENTLY\_USED | Boolean || POPULARITY | Decimal || WEIGHTEDPOPULARITY | Decimal || CLIENTCACHEREFRESHPOLICY | Integer || ENCRYPTION\_LEVEL | String |

## Remarks

Corresponds to the DBSCHEMA\_CATALOGS data management view (DMV).

As all the INFO functions, it cannot be used in calculated tables and calculated columns.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation not available.  
The function may be undocumented or unsupported. Check the Compatibility box on this page.

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
