---
title: "INFO.COLUMNSTORAGES"
function: "info-columnstorages"
category: "Information"
url: "https://dax.guide/info-columnstorages/"
source: "dax.guide"
重要度:
难度:
---

# INFO.COLUMNSTORAGES DAX Function (Information)

Returns a list of all column storages in the current model with columns matching the schema rowset for column storage objects.

## Syntax

INFO.COLUMNSTORAGES ( [<RestrictionName> [, [<RestrictionValue>] [, <RestrictionName> [, [<RestrictionValue>] [, … ] ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| RestrictionName | Optional Repeatable | Restriction name. |
| RestrictionValue | Optional Repeatable | Restriction value. |

## Return values

Table An entire table or a table with one or more columns.

| Field | Type |
| --- | --- |
| ID | Integer || ColumnID | Integer || Name | String || StoragePosition | Integer || DictionaryStorageID | Integer || Settings | Integer || ColumnFlags | Integer || Collation | String || OrderByColumn | String || Locale | Integer || BinaryCharacters | Integer || Statistics\_DistinctStates | Integer || Statistics\_MinDataID | Integer || Statistics\_MaxDataID | Integer || Statistics\_OriginalMinSegmentDataID | Integer || Statistics\_RLESortOrder | Integer || Statistics\_RowCount | Integer || Statistics\_HasNulls | Boolean || Statistics\_RLERuns | Integer || Statistics\_OthersRLERuns | Integer || Statistics\_Usage | Integer || Statistics\_DBType | Integer || Statistics\_XMType | Integer || Statistics\_CompressionType | Integer || Statistics\_CompressionParam | Integer || Statistics\_EncodingHint | Integer || IsDeltaPartitionColumn | Boolean || DeltaColumnMappingPhysicalName | String || DeltaColumnMappingId | Integer || FramedSourceColumn | String |

## Remarks

Corresponds to the TMSCHEMA\_COLUMN\_STORAGES data management view (DMV).

As all the INFO functions, it cannot be used in calculated tables and calculated columns.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation not available.  
The function may be undocumented or unsupported. Check the Compatibility box on this page.

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
