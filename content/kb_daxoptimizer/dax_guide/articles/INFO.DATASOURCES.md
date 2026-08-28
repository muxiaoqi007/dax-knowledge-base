---
title: "INFO.DATASOURCES"
function: "info-datasources"
category: "Information"
url: "https://dax.guide/info-datasources/"
source: "dax.guide"
重要度:
难度:
---

# INFO.DATASOURCES DAX Function (Information)

Represents the TMSCHEMA\_DATASOURCES DMV query function.

## Syntax

INFO.DATASOURCES ( [<RestrictionName> [, [<RestrictionValue>] [, <RestrictionName> [, [<RestrictionValue>] [, … ] ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| RestrictionName | Optional Repeatable | Restriction name. |
| RestrictionValue | Optional Repeatable | Restriction value. |

## Return values

Table An entire table or a table with one or more columns.

| Field | Type |
| --- | --- |
| ID | Integer || ModelID | Integer || Name | String || Description | String || Type | Integer || ConnectionString | String || ImpersonationMode | Integer || Account | String || Password | String || MaxConnections | Integer || Isolation | Integer || Timeout | Integer || Provider | String || ModifiedTime | DateTime || ConnectionDetails | String || Options | String || Credential | String || ContextExpression | String |

## Remarks

Corresponds to the TMSCHEMA\_DATA\_SOURCES data management view (DMV).

As all the INFO functions, it cannot be used in calculated tables and calculated columns.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation not available.  
The function may be undocumented or unsupported. Check the Compatibility box on this page.

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
