---
title: "INFO.STORAGETABLECOLUMNS"
function: "info-storagetablecolumns"
category: "Information"
url: "https://dax.guide/info-storagetablecolumns/"
source: "dax.guide"
重要度:
难度:
---

# INFO.STORAGETABLECOLUMNS DAX Function (Information)

Returns statistics about the columns of in-memory tables.

## Syntax

INFO.STORAGETABLECOLUMNS ( [<RestrictionName> [, [<RestrictionValue>] [, <RestrictionName> [, [<RestrictionValue>] [, … ] ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| RestrictionName | Optional Repeatable | Restriction name. |
| RestrictionValue | Optional Repeatable | Restriction value. |

## Return values

Table An entire table or a table with one or more columns.

| Field | Type |
| --- | --- |
| DATABASE\_NAME | String || CUBE\_NAME | String || MEASURE\_GROUP\_NAME | String || DIMENSION\_NAME | String || ATTRIBUTE\_NAME | String || TABLE\_ID | String || COLUMN\_ID | String || COLUMN\_TYPE | String || COLUMN\_ENCODING | Integer || DATATYPE | String || ISKEY | Boolean || ISUNIQUE | Boolean || ISNULLABLE | Boolean || ISROWNUMBER | Boolean || DICTIONARY\_SIZE | Integer || DICTIONARY\_ISPAGEABLE | Boolean || DICTIONARY\_ISRESIDENT | Boolean || DICTIONARY\_TEMPERATURE | Decimal || DICTIONARY\_LAST\_ACCESSED | DateTime |

## Remarks

Corresponds to the DISCOVER\_STORAGE\_TABLE\_COLUMNS data management view (DMV).

As all the INFO functions, it cannot be used in calculated tables and calculated columns.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation not available.  
The function may be undocumented or unsupported. Check the Compatibility box on this page.

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
