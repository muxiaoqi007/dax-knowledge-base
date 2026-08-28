---
title: "INFO.MODEL"
function: "info-model"
category: "Information"
url: "https://dax.guide/info-model/"
source: "dax.guide"
重要度:
难度:
---

# INFO.MODEL DAX Function (Information)

Represents the TMSCHEMA\_MODEL DMV query function.

## Syntax

INFO.MODEL ( [<RestrictionName> [, [<RestrictionValue>] [, <RestrictionName> [, [<RestrictionValue>] [, … ] ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| RestrictionName | Optional Repeatable | Restriction name. |
| RestrictionValue | Optional Repeatable | Restriction value. |

## Return values

Table An entire table or a table with one or more columns.

| Field | Type |
| --- | --- |
| ID | Integer || Name | String || Description | String || StorageLocation | String || DefaultMode | Integer || DefaultDataView | Integer || Culture | String || Collation | String || ModifiedTime | DateTime || StructureModifiedTime | DateTime || Version | Integer || DataAccessOptions | String || DefaultMeasureID | Integer || DefaultPowerBIDataSourceVersion | Integer || ForceUniqueNames | Boolean || DiscourageImplicitMeasures | Boolean || DataSourceVariablesOverrideBehavior | Integer || DataSourceDefaultMaxConnections | Integer || SourceQueryCulture | String || MAttributes | String || DiscourageCompositeModels | Boolean || AutomaticAggregationOptions | String || DisableAutoExists | Integer || MaxParallelismPerRefresh | Integer || MaxParallelismPerQuery | Integer || DirectLakeBehavior | Integer || ValueFilterBehavior | Integer |

## Remarks

Corresponds to the TMSCHEMA\_MODEL data management view (DMV).

As all the INFO functions, it cannot be used in calculated tables and calculated columns.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation not available.  
The function may be undocumented or unsupported. Check the Compatibility box on this page.

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
