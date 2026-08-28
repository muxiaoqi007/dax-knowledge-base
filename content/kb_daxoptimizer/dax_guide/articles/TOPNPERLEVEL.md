---
title: "TOPNPERLEVEL"
function: "topnperlevel"
category: "Table manipulation"
url: "https://dax.guide/topnperlevel/"
source: "dax.guide"
重要度:
难度:
---

# TOPNPERLEVEL DAX Function (Table manipulation)

## Syntax

TOPNPERLEVEL ( <Rows>, <Table>, <LevelsDefinition>, <NodesExpanded>, <LevelsBoundaries>, <RestartIndicatorColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Rows |  | Number of rows to return for each level |
| Table |  | Table expression containing the columns used to define logical levels |
| LevelsDefinition |  | Table containing rows defining the levels considering in the logical filter by level. Every row has 4 arguments: LevelNumber, TotalColumnName, ColumnName, FlagOrder  FlagOrder is 1 for visible columns and 0 for columns defining the sort order of the level |
| NodesExpanded |  | Table containing rows defining the items selected for each level. Every row has 4 arguments: Unknown, LevelNumber, ColumnName, ItemSelected |
| LevelsBoundaries |  | Table containing rows defining the boundaries for each level. Every row has 5 arguments: Unknown, LevelNumber, ColumnName, BoundaryItemValue, BoundaryType  BoundaryType is 1 for the last item displayed in the level and 2 for the first item displayed in the level |
| RestartIndicatorColumnName |  | Name of the column added to the result containing to indicate the presence of items before the row (1) or after the row (2). |

## Return values

Table An entire table or a table with one or more columns.

The table returned is not sorted, it is just filtered according to the required parameters.  
The sort order of the result depends on the ORDER BY condition of EVALUATE.

## Remarks

This is an undocumented extension function for Power BI that is not included in the Analysis Services engine.

## Related functions

Other related functions are:

- [[TOPN]]
- [[TOPNSKIP]]

Last update: Feb 18, 2020   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation not available.  
The function may be undocumented or unsupported. Check the Compatibility box on this page.

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
