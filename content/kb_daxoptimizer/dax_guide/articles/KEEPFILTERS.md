---
title: "KEEPFILTERS"
function: "keepfilters"
category: "Filter"
url: "https://dax.guide/keepfilters/"
source: "dax.guide"
重要度:
难度:
---

# KEEPFILTERS DAX Function (Filter)

Changes the [[CALCULATE]] and [[CALCULATETABLE]] function filtering semantics.

## Syntax

KEEPFILTERS ( <Expression> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Expression |  | [[CALCULATE]] or [[CALCULATETABLE]] function expression or filter. |

## Return values

Even though KEEPFILTERS could be considered a table function, as a filter modifier it changes the behavior of a predicate or of the table expression provided as an argument. Therefore, it cannot be considered a function returning a value.

## Remarks

KEEPFILTERS is a filter modifier that does not remove an existing column or table filter in the filter context that conflicts with the filter applied by the argument of KEEPFILTERS used as:

- a filter argument in [[CALCULATE]] / [[CALCULATETABLE]]
- an argument of an iterator used in a following context transition

[» 13 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

In the following example, KEEPFILTERS is used as a filter modifier in CALCULATE. The Always Red Sales measure returns always the sum of the Red products, overriding any existing filter over the Color column. The measure Only Red Sales return the sales of red products within the set of selected colors. If Red is not included in the current filter context, the result is blank.

```dax


Always Red Sales :=

CALCULATE (

    [Sales Amount],

    'Product'[Color] = "Red"

)



Only Red Sales :=

CALCULATE (

    [Sales Amount],

    KEEPFILTERS ( 'Product'[Color] = "Red" )

)

```

When KEEPFILTERS is used for an iterator, it keeps the existing filter context when there is a context transition. The measure Average Sales Only Trendy Colors computes the average Sales of the colors included within the Trendy Colors, without considering those that are not in the current filter context. If the measure is evaluated in a filter context that has a filter over Red, Yellow, and White, the result will average only Red and White, ignoring Yellow and Blue colors.

```dax


Average Sales Only Trendy Colors :=

VAR TrendyColors =

    TREATAS (

        { "Red", "Blue", "White" },

        'Product'[Color]

    )

RETURN

    AVERAGEX (

        KEEPFILTERS ( TrendyColors ),

        [Sales Amount]

    )

```

```dax


-- Compare a White color filter with and without KEEPFILTERS

DEFINE 

    MEASURE Sales[White Sales] = 

        CALCULATE ( 

            [Sales Amount], 

            Product[Color] = "White" 

        )

    MEASURE Sales[White Sales Keep] = 

        CALCULATE ( 

            [Sales Amount], 

            KEEPFILTERS ( Product[Color] = "White" )

        )

EVALUATE

    ADDCOLUMNS (

        VALUES ( 'Product'[Color] ),

        "Sales Amount", [Sales Amount],

        "White Sales", [White Sales],

        "White Sales Keep", [White Sales Keep]

    )

ORDER BY [Sales Amount] DESC

```

| Product[Color] | Sales Amount | White Sales | White Sales Keep |
| --- | --- | --- | --- |
| Silver | 6,798,560.86 | 5,829,599.91 | (Blank) |
| Black | 5,860,066.14 | 5,829,599.91 | (Blank) |
| White | 5,829,599.91 | 5,829,599.91 | 5,829,599.91 |
| Grey | 3,509,138.09 | 5,829,599.91 | (Blank) |
| Blue | 2,435,444.62 | 5,829,599.91 | (Blank) |
| Green | 1,403,184.38 | 5,829,599.91 | (Blank) |
| Red | 1,110,102.10 | 5,829,599.91 | (Blank) |
| Brown | 1,029,508.95 | 5,829,599.91 | (Blank) |
| Orange | 857,320.28 | 5,829,599.91 | (Blank) |
| Pink | 828,638.54 | 5,829,599.91 | (Blank) |
| Silver Grey | 371,908.92 | 5,829,599.91 | (Blank) |
| Gold | 361,496.01 | 5,829,599.91 | (Blank) |
| Azure | 97,389.89 | 5,829,599.91 | (Blank) |
| Yellow | 89,715.56 | 5,829,599.91 | (Blank) |
| Purple | 5,973.84 | 5,829,599.91 | (Blank) |
| Transparent | 3,295.89 | 5,829,599.91 | (Blank) |

```dax


--  KEEPFILTERS can be used as a modifier in the table 

--  arguments of iterators. In that case, it changes the 

--  way CALCULATE merges filters during context transition

--  over the iterated columns.

DEFINE

    MEASURE Sales[SalesX] =

        SUMX ( VALUES ( Product[Color] ), [Sales Amount] )

    MEASURE Sales[SalesX Keep] =

        SUMX ( KEEPFILTERS ( VALUES ( Product[Color] ) ), [Sales Amount] )

    VAR YearsAndColor =

        TREATAS (

            { ( "CY 2008", "Red" ), ( "CY 2007", "White" ) },

            'Date'[Calendar Year],

            'Product'[Color]

        )

EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Color],

    YearsAndColor,

    "Sales Amount", [Sales Amount],

    "SalesX", [SalesX],

    "SalesX Keep", [SalesX Keep]

)

ORDER BY [Sales Amount] DESC

```

| Product[Color] | Sales Amount | SalesX | SalesX Keep |
| --- | --- | --- | --- |
| White | 2,002,452.65 | 2,368,479.81 | 2,002,452.65 |
| Red | 395,277.22 | 2,288,307.38 | 395,277.22 |

## Related articles

Learn more about KEEPFILTERS in the following articles:

- [**Filter Arguments in CALCULATE**](https://www.sqlbi.com/articles/filter-arguments-in-calculate/)

  A filter argument in CALCULATE is always an iterator. Finding the right granularity for it is important to control the result and the performance. This article describes the options available to create complex filters in DAX. [» Read more](https://www.sqlbi.com/articles/filter-arguments-in-calculate/)
- [**KEEPFILTERS: a new DAX feature to correctly compute over arbitrary shaped sets**](https://www.sqlbi.com/articles/keepfilters-a-new-dax-feature-to-correctly-compute-over-arbitrary-shaped-sets/)

  Having read this question on the mdsn blogs, I investigated on the KEEPFILTERS function and, after having learned it, it is now time to write about it. Moreover, before start to write about it, I need to thank the dev team… [» Read more](https://www.sqlbi.com/articles/keepfilters-a-new-dax-feature-to-correctly-compute-over-arbitrary-shaped-sets/)
- [**Best Practices Using SUMMARIZE and ADDCOLUMNS**](https://www.sqlbi.com/articles/best-practices-using-summarize-and-addcolumns/)

  This article provides the best practice to use ADDCOLUMNS and SUMMARIZE, two functions that can be used in any DAX expression, including measures. [» Read more](https://www.sqlbi.com/articles/best-practices-using-summarize-and-addcolumns/)
- [**Using tuple syntax in DAX expressions**](https://www.sqlbi.com/articles/using-tuple-syntax-in-dax-expressions/)

  This article describes the use of the tuple syntax in DAX expressions to simplify comparisons involving two or more columns. [» Read more](https://www.sqlbi.com/articles/using-tuple-syntax-in-dax-expressions/)
- [**Preparing a data model for Sankey Charts in Power BI**](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)

  This article describes how to correctly shape a data model and prepare data to use a Sankey Chart as a funnel, considering events related to a customer (contact, trial, subscription, renewal, and others). [» Read more](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)
- [**Using KEEPFILTERS in DAX**](https://www.sqlbi.com/articles/using-keepfilters-in-dax-updated/)

  This article explains how to use KEEPFILTERS to intersect instead of override an existing filter context in DAX. Using KEEPFILTERS simplifies the code and improves performance. [» Read more](https://www.sqlbi.com/articles/using-keepfilters-in-dax-updated/)
- [**Introducing CALCULATE in DAX**](https://www.sqlbi.com/articles/introducing-calculate-in-dax/)

  CALCULATE is the most powerful and complex function in DAX. In this article, we provide an introduction to CALCULATE, its behavior, and how to use it. [» Read more](https://www.sqlbi.com/articles/introducing-calculate-in-dax/)
- [**When to use KEEPFILTERS over iterators**](https://www.sqlbi.com/articles/when-to-use-keepfilters-over-iterators/)

  This article describes how to use KEEPFILTERS in DAX iterator functions to preserve arbitrarily shaped filters in context transition. [» Read more](https://www.sqlbi.com/articles/when-to-use-keepfilters-over-iterators/)
- [**Specifying multiple filter conditions in CALCULATE**](https://www.sqlbi.com/articles/specifying-multiple-filter-conditions-in-calculate/)

  This article introduces the new DAX syntax (March 2021) to support CALCULATE filter predicates that reference multiple columns from the same table. [» Read more](https://www.sqlbi.com/articles/specifying-multiple-filter-conditions-in-calculate/)
- [**Best practices for using KEEPFILTERS in DAX**](https://www.sqlbi.com/articles/best-practices-for-using-keepfilters-in-dax/)

  This article describes the best practices for deciding when to use (and when not to use) KEEPFILTERS in CALCULATE filter arguments. [» Read more](https://www.sqlbi.com/articles/best-practices-for-using-keepfilters-in-dax/)
- [**Filter context in DAX explained visually**](https://www.sqlbi.com/articles/filter-context-in-dax-explained-visually/)

  This article describes the DAX filter context using a conceptual model based on a visual representation. [» Read more](https://www.sqlbi.com/articles/filter-context-in-dax-explained-visually/)
- [**Filter columns, not tables, in DAX**](https://www.sqlbi.com/articles/filter-columns-not-tables-in-dax/)

  One of the few golden rules in DAX is to always filter columns and never filter tables with CALCULATE. This article explains the rationale behind the rule. [» Read more](https://www.sqlbi.com/articles/filter-columns-not-tables-in-dax/)
- [**Context transition in DAX explained visually**](https://www.sqlbi.com/articles/context-transition-in-dax-explained-visually/)

  This article describes the DAX context transition using a conceptual model based on a visual representation. [» Read more](https://www.sqlbi.com/articles/context-transition-in-dax-explained-visually/)

## Related functions

Other related functions are:

- [[CALCULATE]]
- [[CALCULATETABLE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/keepfilters-function-dax](https://docs.microsoft.com/en-us/dax/keepfilters-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
