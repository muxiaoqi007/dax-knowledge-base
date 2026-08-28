---
title: "INFO.FUNCTIONS"
function: "info-functions"
category: "Information"
url: "https://dax.guide/info-functions/"
source: "dax.guide"
重要度:
难度:
---

# INFO.FUNCTIONS DAX Function (Information)

Returns information about the functions that are currently available for use in the DAX programming language.

## Syntax

INFO.FUNCTIONS ( [<RestrictionName> [, [<RestrictionValue>] [, <RestrictionName> [, [<RestrictionValue>] [, … ] ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| RestrictionName | Optional Repeatable | Restriction name. |
| RestrictionValue | Optional Repeatable | Restriction value. |

## Return values

Table An entire table or a table with one or more columns.

| Field | Type |
| --- | --- |
| FUNCTION\_NAME | String || DESCRIPTION | String || PARAMETER\_LIST | String || RETURN\_TYPE | Integer || ORIGIN | Integer || INTERFACE\_NAME | String || LIBRARY\_NAME | String || DLL\_NAME | String || HELP\_FILE | String || HELP\_CONTEXT | Integer || OBJECT | String || CAPTION | String || PARAMETERINFO | Integer || DIRECTQUERY\_PUSHABLE | Integer || VISUAL\_CALCULATIONS\_INFO | Integer |

## Remarks

As all the INFO functions, it cannot be used in calculated tables and calculated columns.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation not available.  
The function may be undocumented or unsupported. Check the Compatibility box on this page.

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
