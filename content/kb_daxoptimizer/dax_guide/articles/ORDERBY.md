---
title: "ORDERBY"
function: "orderby"
category: "Filter"
url: "https://dax.guide/orderby/"
source: "dax.guide"
重要度:
难度:
---

# ORDERBY DAX Function (Filter)

The expressions and order directions used to determine the sort order within each partition. Can only be used within a Window function.

## Syntax

ORDERBY ( [<OrderBy\_Expression> [, [<OrderBy\_Direction>] [, <OrderBy\_Expression> [, [<OrderBy\_Direction>] [, … ] ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| OrderBy\_Expression | Optional Repeatable | An expression used to determine the sort order. |
| OrderBy\_Direction | Optional Repeatable | Specifies how to sort *OrderBy\_ColumnName* values. It is a two-part value of the form *OrderDirection [BlankHandling]*.  *OrderDirection* specifies how to sort *orderBy\_expression* values (i.e. ascending or descending). Valid values include:   - **DESC**. Alternative value: **0**(zero)/**FALSE**. Sorts in descending order of values of *OrderBy\_ColumnName*. - **ASC**. Alternative value: **1**/**TRUE**. Sorts in ascending order of values of *OrderBy\_ColumnName* . This is the default value if *OrderBy\_Direction* is omitted.   *BlankHandling* part is optional. It specifies how blanks are ordered. Valid values include:   - **BLANKS DEFAULT**. This is the default value. The behavior for numerical values is blank values are ordered between zero and negative values. The behavior for strings is blank values are ordered before all strings, including empty strings. - **BLANKS [[FIRST]]**. Blanks are always ordered on the beginning, regardless of ascending or descending sorting order. - **BLANKS [[LAST]]**. Blanks are always ordered on the end, regardless of ascending or descending sorting order. |

## Return values

This function does not return a value.

## Remarks

This function can only be used within a window function expression ([[INDEX]], [[OFFSET]], [[WINDOW]]).

## Related articles

Learn more about ORDERBY in the following articles:

- [**Introducing window functions in DAX**](https://www.sqlbi.com/articles/introducing-window-functions-in-dax/)

  In December 2022, DAX was enriched with window functions: INDEX, OFFSET, and WINDOW. This article introduces the syntax and the basic functionalities of these new features. [» Read more](https://www.sqlbi.com/articles/introducing-window-functions-in-dax/)
- [**Preparing a data model for Sankey Charts in Power BI**](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)

  This article describes how to correctly shape a data model and prepare data to use a Sankey Chart as a funnel, considering events related to a customer (contact, trial, subscription, renewal, and others). [» Read more](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)
- [**Introducing the RANK window function in DAX**](https://www.sqlbi.com/articles/introducing-the-rank-window-function-in-dax/)

  RANK is a new DAX function to rank items based on multiple columns. This article introduces the RANK function and its differences with RANKX. [» Read more](https://www.sqlbi.com/articles/introducing-the-rank-window-function-in-dax/)
- [**Introducing VISUAL SHAPE for visual calculations in Power BI**](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/)

  This article introduces the VISUAL SHAPE clause, which defines a hierarchical structure for a table used in visual calculations. [» Read more](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/)
- [**Using EXPAND and COLLAPSE in visual calculations**](https://www.sqlbi.com/articles/using-expand-and-collapse-in-visual-calculations/)

  This article provides examples of visual calculations where the use of EXPAND and COLLAPSE is required to obtain the correct result. [» Read more](https://www.sqlbi.com/articles/using-expand-and-collapse-in-visual-calculations/)
- [**Understanding apply semantics for window functions in DAX**](https://www.sqlbi.com/articles/understanding-apply-semantics-for-window-functions-in-dax/)

  This article explains the unique behavior of apply semantics: a new way of computing table expressions when multiple rows are selected in DAX window functions. [» Read more](https://www.sqlbi.com/articles/understanding-apply-semantics-for-window-functions-in-dax/)
- [**Dynamic Pareto analysis in Power BI**](https://www.sqlbi.com/articles/dynamic-pareto-analysis-in-power-bi/)

  This article describes how to implement a dynamic Pareto calculation in Power BI based on a measure that can be selected from a slicer and dynamically filtered by other slicers in the report. [» Read more](https://www.sqlbi.com/articles/dynamic-pareto-analysis-in-power-bi/)

## Related functions

Other related functions are:

- [[INDEX]]
- [[OFFSET]]
- [[WINDOW]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Daniil Maslyuk

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/orderby-function-dax](https://learn.microsoft.com/en-us/dax/orderby-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
