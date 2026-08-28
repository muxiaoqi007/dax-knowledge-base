---
title: "INFO.OBJECTTRANSLATIONS"
function: "info-objecttranslations"
category: "Information"
url: "https://dax.guide/info-objecttranslations/"
source: "dax.guide"
重要度:
难度:
---

# INFO.OBJECTTRANSLATIONS DAX Function (Information)

Returns a list of all object translations in the current model with columns matching the schema rowset for object translation objects.

## Syntax

INFO.OBJECTTRANSLATIONS ( [<RestrictionName> [, [<RestrictionValue>] [, <RestrictionName> [, [<RestrictionValue>] [, … ] ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| RestrictionName | Optional Repeatable | Restriction name. |
| RestrictionValue | Optional Repeatable | Restriction value. |

## Return values

Table An entire table or a table with one or more columns.

| Field | Type |
| --- | --- |
| ID | Integer || CultureID | Integer || ObjectID | Integer || ObjectType | Integer || Property | Integer || Value | String || ModifiedTime | DateTime || Altered | Boolean |

## Remarks

Corresponds to the TMSCHEMA\_OBJECT\_TRANSLATIONS data management view (DMV).

As all the INFO functions, it cannot be used in calculated tables and calculated columns.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation not available.  
The function may be undocumented or unsupported. Check the Compatibility box on this page.

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
