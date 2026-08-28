---
title: "SAMPLEAXISWITHLOCALMINMAX"
function: "sampleaxiswithlocalminmax"
category: "Table manipulation"
url: "https://dax.guide/sampleaxiswithlocalminmax/"
source: "dax.guide"
重要度:
难度:
---

# SAMPLEAXISWITHLOCALMINMAX DAX Function (Table manipulation)

Returns a subset from a table expression that is obtained by binning the primary X-Axis into equal-sized bins and then preserving the local min/max for each bin across different series.

## Syntax

SAMPLEAXISWITHLOCALMINMAX ( <Size>, <Table>, <Axis>, <Measure> [, <Measure> [, … ] ], <MinResolution> [, <DynamicSeries> [, <DynamicSeries> [, … ] ] [, <DynamicSeriesSelectionCriteria>] [, <DynamicSeriesSelectionOrder>] [, <MaxResolution>] [, <MaxDynamicSeries>] [, <MaxIterations>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Size |  | A positive integer specifying the number of rows the function should attempt to return. It is not guaranteed to return this number of records since the source table could have fewer rows, or the resolution of iterations parameters could be set such that the desired number of rows cannot be achieved. |
| Table |  | A table expression that represents the input to the function. The function will return a table with the same columns (including lineage) as the input. |
| Axis |  | The (numeric or date) column that should be binned. Each bin will preserve rows that contain min/max measure value per series. This is the column that will be sampled to find an evenly distributed set of values according to the DesiredRowsCount and Resolution parameters. |
| Measure | Repeatable | The (numeric or date) column(s) that will be sampled to find a representative set of values from Axis that include local minimum or maximum values of the Measure columns. |
| MinResolution |  | For any series, the minimum number of selected rows across the global Axis range. |
| DynamicSeries | Optional Repeatable | The column(s) from the specified table that plays the role of a series. |
| DynamicSeriesSelectionCriteria | Optional | Can be NONE or ALPHABETICAL. |
| DynamicSeriesSelectionOrder | Optional | The order to be applied. 0/FALSE/DESC – descending; 1/TRUE/ASC – ascending. |
| MaxResolution | Optional | For any series, the maximum number of selected rows across the global Axis range. |
| MaxDynamicSeries | Optional | The maximum number of distinct dynamic series allowed. |
| MaxIterations | Optional | Number of iterations with different bin-sizes to try in order to satisfy the desired number of requested rows. |

## Return values

Table An entire table or a table with one or more columns.

A subset from a table expression that preserves the local min/max for each bin across different series.

## Remarks

SAMPLEAXISWITHLOCALMINMAX is used by Power BI to reduce the number of points in a line chart with a continuous (numeric) x-axis.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Daniel Otykier

Microsoft documentation not available.  
The function may be undocumented or unsupported. Check the Compatibility box on this page.

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
