---
title: "INFO.USERDEFINEDFUNCTIONS"
function: "info-userdefinedfunctions"
category: "Information"
url: "https://dax.guide/info-userdefinedfunctions/"
source: "dax.guide"
重要度:
难度:
---

# INFO.USERDEFINEDFUNCTIONS DAX Function (Information)

Represents the TMSCHEMA\_FUNCTIONS DMV query function.

## Syntax

INFO.USERDEFINEDFUNCTIONS ( [<RestrictionName> [, [<RestrictionValue>] [, <RestrictionName> [, [<RestrictionValue>] [, … ] ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| RestrictionName | Optional Repeatable | Restriction name. |
| RestrictionValue | Optional Repeatable | Restriction value. |

## Return values

Table An entire table or a table with one or more columns.

Corresponds to the TMSCHEMA\_FUNCTIONS data management view (DMV).

As with all the INFO functions, it cannot be used in calculated tables and calculated columns.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation not available.  
The function may be undocumented or unsupported. Check the Compatibility box on this page.

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
