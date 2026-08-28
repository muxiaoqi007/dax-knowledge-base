---
title: "INFO.CALCDEPENDENCY"
function: "info-calcdependency"
category: "Information"
url: "https://dax.guide/info-calcdependency/"
source: "dax.guide"
重要度:
难度:
---

# INFO.CALCDEPENDENCY DAX Function (Information)

Returns information about the calculation dependency for a DAX query.

## Syntax

INFO.CALCDEPENDENCY ( [<RestrictionName> [, [<RestrictionValue>] [, <RestrictionName> [, [<RestrictionValue>] [, … ] ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| RestrictionName | Optional Repeatable | Restriction name. |
| RestrictionValue | Optional Repeatable | Restriction value. |

## Return values

Table An entire table or a table with one or more columns.

| Field | Type |
| --- | --- |
| DATABASE\_NAME | String || OBJECT\_TYPE | String || TABLE | String || OBJECT | String || EXPRESSION | String || REFERENCED\_OBJECT\_TYPE | String || REFERENCED\_TABLE | String || REFERENCED\_OBJECT | String || REFERENCED\_EXPRESSION | String || QUERY | String |

## Remarks

Corresponds to the DISCOVER\_CALC\_DEPENDENCY data management view (DMV).

As all the INFO functions, it cannot be used in calculated tables and calculated columns.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation not available.  
The function may be undocumented or unsupported. Check the Compatibility box on this page.

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
