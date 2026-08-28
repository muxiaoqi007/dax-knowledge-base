---
title: "PARTITIONBY"
function: "partitionby"
category: "Filter"
url: "https://dax.guide/partitionby/"
source: "dax.guide"
重要度:
难度:
---

# PARTITIONBY DAX Function (Filter)

The columns used to determine how to partition the data. Can only be used within a Window function.

## Syntax

PARTITIONBY ( [<PartitionBy\_ColumnName> [, <PartitionBy\_ColumnName> [, … ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| PartitionBy\_ColumnName | Optional Repeatable | The name of an existing column to be used to partition the window function’s *Relation*. |

## Return values

This function does not return a value.

## Remarks

This function can only be used within a window function expression (like [[INDEX]], [[OFFSET]], [[WINDOW]], [[RANK]], and [[ROWNUMBER]]).

The arguments PartitionBy\_ColumnName must be model columns with a valid data lineage. You cannot use columns computed in DAX expressions for PartitionBy\_ColumnName.

## Related articles

Learn more about PARTITIONBY in the following articles:

- [**Introducing window functions in DAX**](https://www.sqlbi.com/articles/introducing-window-functions-in-dax/)

  In December 2022, DAX was enriched with window functions: INDEX, OFFSET, and WINDOW. This article introduces the syntax and the basic functionalities of these new features. [» Read more](https://www.sqlbi.com/articles/introducing-window-functions-in-dax/)
- [**Preparing a data model for Sankey Charts in Power BI**](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)

  This article describes how to correctly shape a data model and prepare data to use a Sankey Chart as a funnel, considering events related to a customer (contact, trial, subscription, renewal, and others). [» Read more](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)
- [**Introducing VISUAL SHAPE for visual calculations in Power BI**](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/)

  This article introduces the VISUAL SHAPE clause, which defines a hierarchical structure for a table used in visual calculations. [» Read more](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/)
- [**Understanding apply semantics for window functions in DAX**](https://www.sqlbi.com/articles/understanding-apply-semantics-for-window-functions-in-dax/)

  This article explains the unique behavior of apply semantics: a new way of computing table expressions when multiple rows are selected in DAX window functions. [» Read more](https://www.sqlbi.com/articles/understanding-apply-semantics-for-window-functions-in-dax/)
- [**SQLBI+ updates in May 2023**](https://www.sqlbi.com/blog/marco/2024/05/27/sqlbi-updates-in-may-2023/)

  In 2023, we released the first draft of the Window functions in DAX whitepaper as part of SQLBI+. Since then, we have released a few updates and are now glad to announce the availability of the related 3-hour video course… [» Read more](https://www.sqlbi.com/blog/marco/2024/05/27/sqlbi-updates-in-may-2023/)
- [**Account receivable aging in Power BI**](https://www.sqlbi.com/articles/account-receivable-aging-in-power-bi/)

  This article describes an Accounts Receivable Aging report in Power BI, and shows how to simplify a business problem using existing modeling patterns. [» Read more](https://www.sqlbi.com/articles/account-receivable-aging-in-power-bi/)
- [**Dynamic Pareto analysis in Power BI**](https://www.sqlbi.com/articles/dynamic-pareto-analysis-in-power-bi/)

  This article describes how to implement a dynamic Pareto calculation in Power BI based on a measure that can be selected from a slicer and dynamically filtered by other slicers in the report. [» Read more](https://www.sqlbi.com/articles/dynamic-pareto-analysis-in-power-bi/)

## Related functions

Other related functions are:

- [[INDEX]]
- [[OFFSET]]
- [[WINDOW]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Denis Selimovic

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/partitionby-function-dax](https://learn.microsoft.com/en-us/dax/partitionby-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
