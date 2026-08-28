---
title: "INFO.COLUMNS"
function: "info-columns"
category: "Information"
url: "https://dax.guide/info-columns/"
source: "dax.guide"
重要度:
难度:
---

# INFO.COLUMNS DAX Function (Information)

Returns a list of all columns in the current model with columns matching the schema rowset for column objects.

## Syntax

INFO.COLUMNS ( [<RestrictionName> [, [<RestrictionValue>] [, <RestrictionName> [, [<RestrictionValue>] [, … ] ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| RestrictionName | Optional Repeatable | Restriction name. |
| RestrictionValue | Optional Repeatable | Restriction value. |

## Return values

Table An entire table or a table with one or more columns.

| Field | Type |
| --- | --- |
| ID | Integer || TableID | Integer || ExplicitName | String || InferredName | String || ExplicitDataType | Integer || InferredDataType | Integer || DataCategory | String || Description | String || IsHidden | Boolean || State | Integer || IsUnique | Boolean || IsKey | Boolean || IsNullable | Boolean || Alignment | Integer || TableDetailPosition | Integer || IsDefaultLabel | Boolean || IsDefaultImage | Boolean || SummarizeBy | Integer || ColumnStorageID | Integer || Type | Integer || SourceColumn | String || ColumnOriginID | Integer || Expression | String || FormatString | String || IsAvailableInMDX | Boolean || SortByColumnID | Integer || AttributeHierarchyID | Integer || ModifiedTime | DateTime || StructureModifiedTime | DateTime || RefreshedTime | DateTime || SystemFlags | Integer || KeepUniqueRows | Boolean || DisplayOrdinal | Integer || ErrorMessage | String || SourceProviderType | String || DisplayFolder | String || EncodingHint | Integer || RelatedColumnDetailsID | Integer || AlternateOfID | Integer || LineageTag | String || SourceLineageTag | String |

## Remarks

Corresponds to the TMSCHEMA\_COLUMNS data management view (DMV).

As all the INFO functions, it cannot be used in calculated tables and calculated columns.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation not available.  
The function may be undocumented or unsupported. Check the Compatibility box on this page.

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
