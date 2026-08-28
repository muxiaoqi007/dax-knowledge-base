---
title: "CALCULATE"
function: "calculate"
category: "Filter"
url: "https://dax.guide/calculate/"
source: "dax.guide"
重要度: "5"
难度: "5"
---

# CALCULATE DAX Function (Filter) Context Transition

Evaluates an expression in a context modified by filters.

## Syntax

CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Expression  By Expression |  | The expression to be evaluated. |
| Filter | Optional Repeatable | A boolean (True/False) expression or a table expression that defines a filter. |

## Return values

Scalar A single value of any type.

The value is the result of the expression evaluated in a modified filter context.

## Remarks

Every filter argument can be either a filter removal (such as [[ALL]], [[ALLEXCEPT]], [[ALLNOBLANKROW]]), a filter restore ([[ALLSELECTED]]), or a table expression returning a list of values for one or more columns or for an entire expanded table.

When a filter argument has the form of a predicate with a single column reference, the expression is embedded into a [[FILTER]] expression that filters all the values of the referenced column. For example, the predicate shown in the first expression is internally converted in the second expression.

```dax


CALCULATE ( 

    <expression>,

    table[column] = 10

)



CALCULATE ( 

    <expression>,

    FILTER ( 

        ALL ( table[column] ),

        table[column] = 10

    )

)

```

A filter argument overrides the existing corresponding filters over the same column(s), unless it is embedded within [[KEEPFILTERS]].

CALCULATE evaluation follow these steps:

1. CALCULATE evaluates all the explicit filter arguments in the original evaluation context, each one independently from the others. This includes both the original row contexts (if any) and the original filter context. Once this evaluation is finished, CALCULATE starts building the new filter context.
2. CALCULATE makes a copy of the original filter context to prepare the new filter context. It discards the original row contexts, because the new evaluation context will not contain any row context.
3. CALCULATE performs the context transition. It uses the current value of columns in the original row contexts to provide a filter with a unique value for all the columns currently being iterated in the original row contexts. This filter may or may not contain one individual row. There is no guarantee that the new filter context contains a single row at this point. If there are no row contexts active, this step is skipped. Once all implicit filters created by the context transition are applied to the new filter context, CALCULATE moves on to the next step.
4. CALCULATE evaluates the CALCULATE modifiers used in filter arguments: [[USERELATIONSHIP]], [[CROSSFILTER]], [[ALL]], [[ALLEXCEPT]], [[ALLSELECTED]], and [[ALLNOBLANKROW]]. This step happens after step 3. This is very important, because it means that one can remove the effects of the context transition by using [[ALL]] as a filter argument. The CALCULATE modifiers are applied after the context transition, so they can alter the effects of the context transition.
5. CALCULATE applies the explicit filter arguments evaluated at 1. to the new filter context generated after step 4. These filter arguments are applied to the new filter context once the context transition has happened so they can overwrite it, after filter removal — their filter is not removed by any [[ALL]]\* modifier — and after the relationship architecture has been updated. However, the evaluation of filter arguments happens in the original filter context, and it is not affected by any other modifier or filter within the same CALCULATE function. If a filter argument is modified by [[KEEPFILTERS]], the filter is added to the filter context without overwriting existing filters over the same column(s).

The filter context generated after point (5) is the new filter context used by CALCULATE in the evaluation of its expression.

[» 14 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  The compact syntax (boolean) is expanded in the full syntax 

--  prior to the evaluation

DEFINE

    MEASURE Sales[Red Sales] =

        CALCULATE ( [Sales Amount], 'Product'[Color] = "Red" )

    MEASURE Sales[Red Sales Full] =

        CALCULATE (

            [Sales Amount],

            FILTER ( ALL ( 'Product'[Color] ), 'Product'[Color] = "Red" )

        )

EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Color],

    "Sales Amount", [Sales Amount],

    "Red Sales", [Red Sales],

    "Red Sales Full", [Red Sales Full]

)

```

| Product[Color] | Sales Amount | Red Sales | Red Sales Full |
| --- | --- | --- | --- |
| Silver | 6,798,560.86 | 1,110,102.10 | 1,110,102.10 |
| Blue | 2,435,444.62 | 1,110,102.10 | 1,110,102.10 |
| White | 5,829,599.91 | 1,110,102.10 | 1,110,102.10 |
| Red | 1,110,102.10 | 1,110,102.10 | 1,110,102.10 |
| Black | 5,860,066.14 | 1,110,102.10 | 1,110,102.10 |
| Green | 1,403,184.38 | 1,110,102.10 | 1,110,102.10 |
| Orange | 857,320.28 | 1,110,102.10 | 1,110,102.10 |
| Pink | 828,638.54 | 1,110,102.10 | 1,110,102.10 |
| Yellow | 89,715.56 | 1,110,102.10 | 1,110,102.10 |
| Purple | 5,973.84 | 1,110,102.10 | 1,110,102.10 |
| Brown | 1,029,508.95 | 1,110,102.10 | 1,110,102.10 |
| Grey | 3,509,138.09 | 1,110,102.10 | 1,110,102.10 |
| Gold | 361,496.01 | 1,110,102.10 | 1,110,102.10 |
| Azure | 97,389.89 | 1,110,102.10 | 1,110,102.10 |
| Silver Grey | 371,908.92 | 1,110,102.10 | 1,110,102.10 |
| Transparent | 3,295.89 | 1,110,102.10 | 1,110,102.10 |

```dax


--  You can use any condition as an argument, as long as it can

--  be converted into a table by the DAX engine

DEFINE

    MEASURE Sales[Red Blue Sales] =

        CALCULATE ( [Sales Amount], 'Product'[Color] IN { "Red", "Blue" } )

    MEASURE Sales[Red Blue Sales Full] =

        CALCULATE (

            [Sales Amount],

            FILTER ( ALL ( 'Product'[Color] ), 'Product'[Color] IN { "Red", "Blue" } )

        )

EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Color],

    "Sales Amount", [Sales Amount],

    "Red Blue Sales", [Red Blue Sales],

    "Red Blue Sales Full", [Red Blue Sales Full]

)

```

| Product[Color] | Sales Amount | Red Blue Sales | Red Blue Sales Full |
| --- | --- | --- | --- |
| Silver | 6,798,560.86 | 3,545,546.72 | 3,545,546.72 |
| Blue | 2,435,444.62 | 3,545,546.72 | 3,545,546.72 |
| White | 5,829,599.91 | 3,545,546.72 | 3,545,546.72 |
| Red | 1,110,102.10 | 3,545,546.72 | 3,545,546.72 |
| Black | 5,860,066.14 | 3,545,546.72 | 3,545,546.72 |
| Green | 1,403,184.38 | 3,545,546.72 | 3,545,546.72 |
| Orange | 857,320.28 | 3,545,546.72 | 3,545,546.72 |
| Pink | 828,638.54 | 3,545,546.72 | 3,545,546.72 |
| Yellow | 89,715.56 | 3,545,546.72 | 3,545,546.72 |
| Purple | 5,973.84 | 3,545,546.72 | 3,545,546.72 |
| Brown | 1,029,508.95 | 3,545,546.72 | 3,545,546.72 |
| Grey | 3,509,138.09 | 3,545,546.72 | 3,545,546.72 |
| Gold | 361,496.01 | 3,545,546.72 | 3,545,546.72 |
| Azure | 97,389.89 | 3,545,546.72 | 3,545,546.72 |
| Silver Grey | 371,908.92 | 3,545,546.72 | 3,545,546.72 |
| Transparent | 3,295.89 | 3,545,546.72 | 3,545,546.72 |

```dax


--  The KEEPFILTERS modifier does not remove an existing filter

DEFINE

    MEASURE Sales[Red Blue Sales Keepfilters] =

        CALCULATE ( 

            [Sales Amount], 

            KEEPFILTERS ( 'Product'[Color] IN { "Red", "Blue" } )

        )

    MEASURE Sales[Red Blue Sales] =

        CALCULATE ( 

            [Sales Amount], 

            'Product'[Color] IN { "Red", "Blue" }

        )

EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Color],

    "Sales Amount", [Sales Amount],

    "Red Blue Sales", [Red Blue Sales],

    "Red Blue Sales Keepfilters", [Red Blue Sales Keepfilters]

)

```

| Product[Color] | Sales Amount | Red Blue Sales | Red Blue Sales Keepfilters |
| --- | --- | --- | --- |
| Silver | 6,798,560.86 | 3,545,546.72 | (Blank) |
| Blue | 2,435,444.62 | 3,545,546.72 | 2,435,444.62 |
| White | 5,829,599.91 | 3,545,546.72 | (Blank) |
| Red | 1,110,102.10 | 3,545,546.72 | 1,110,102.10 |
| Black | 5,860,066.14 | 3,545,546.72 | (Blank) |
| Green | 1,403,184.38 | 3,545,546.72 | (Blank) |
| Orange | 857,320.28 | 3,545,546.72 | (Blank) |
| Pink | 828,638.54 | 3,545,546.72 | (Blank) |
| Yellow | 89,715.56 | 3,545,546.72 | (Blank) |
| Purple | 5,973.84 | 3,545,546.72 | (Blank) |
| Brown | 1,029,508.95 | 3,545,546.72 | (Blank) |
| Grey | 3,509,138.09 | 3,545,546.72 | (Blank) |
| Gold | 361,496.01 | 3,545,546.72 | (Blank) |
| Azure | 97,389.89 | 3,545,546.72 | (Blank) |
| Silver Grey | 371,908.92 | 3,545,546.72 | (Blank) |
| Transparent | 3,295.89 | 3,545,546.72 | (Blank) |

```dax


--  When CALCULATE is executed in a row context, it transforms 

--  the row contexts in equivalent filter contexts

DEFINE

    MEASURE Sales[Yearly Avg] =

        AVERAGEX (

            VALUES ( 'Date'[Calendar Year] ),

            CALCULATE ( 

                SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

            )

        )

EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Color],

    "Sales Amount", [Sales Amount],

    "Yearly Avg", [Yearly Avg]

)

```

| Product[Color] | Sales Amount | Yearly Avg |
| --- | --- | --- |
| Silver | 6,798,560.86 | 2,266,186.95 |
| Blue | 2,435,444.62 | 811,814.87 |
| White | 5,829,599.91 | 1,943,199.97 |
| Red | 1,110,102.10 | 370,034.03 |
| Black | 5,860,066.14 | 1,953,355.38 |
| Green | 1,403,184.38 | 467,728.13 |
| Orange | 857,320.28 | 285,773.43 |
| Pink | 828,638.54 | 276,212.85 |
| Yellow | 89,715.56 | 29,905.19 |
| Purple | 5,973.84 | 1,991.28 |
| Brown | 1,029,508.95 | 343,169.65 |
| Grey | 3,509,138.09 | 1,169,712.70 |
| Gold | 361,496.01 | 120,498.67 |
| Azure | 97,389.89 | 32,463.30 |
| Silver Grey | 371,908.92 | 123,969.64 |
| Transparent | 3,295.89 | 1,098.63 |

```dax


--  CALCULATE is implicitly added to any measure reference

DEFINE

    MEASURE Sales[Sales Amount] = 

        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

    MEASURE Sales[Yearly Avg] =

        AVERAGEX (

            VALUES ( 'Date'[Calendar Year] ),

            CALCULATE ( 

                SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

            )

        )

    MEASURE Sales[Yearly Avg 2] =

        AVERAGEX (

            VALUES ( 'Date'[Calendar Year] ),

            [Sales Amount]

        )

EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Color],

    "Sales Amount", [Sales Amount],

    "Yearly Avg", [Yearly Avg],

    "Yearly Avg 2", [Yearly Avg 2]

)

```

| Product[Color] | Sales Amount | Yearly Avg | Yearly Avg 2 |
| --- | --- | --- | --- |
| Silver | 6,798,560.86 | 2,266,186.95 | 2,266,186.95 |
| Blue | 2,435,444.62 | 811,814.87 | 811,814.87 |
| White | 5,829,599.91 | 1,943,199.97 | 1,943,199.97 |
| Red | 1,110,102.10 | 370,034.03 | 370,034.03 |
| Black | 5,860,066.14 | 1,953,355.38 | 1,953,355.38 |
| Green | 1,403,184.38 | 467,728.13 | 467,728.13 |
| Orange | 857,320.28 | 285,773.43 | 285,773.43 |
| Pink | 828,638.54 | 276,212.85 | 276,212.85 |
| Yellow | 89,715.56 | 29,905.19 | 29,905.19 |
| Purple | 5,973.84 | 1,991.28 | 1,991.28 |
| Brown | 1,029,508.95 | 343,169.65 | 343,169.65 |
| Grey | 3,509,138.09 | 1,169,712.70 | 1,169,712.70 |
| Gold | 361,496.01 | 120,498.67 | 120,498.67 |
| Azure | 97,389.89 | 32,463.30 | 32,463.30 |
| Silver Grey | 371,908.92 | 123,969.64 | 123,969.64 |
| Transparent | 3,295.89 | 1,098.63 | 1,098.63 |

```dax


--  CALCULATE evaluation steps:

--      1. Evaluation of filter arguments

--      2. Context transition

--      3. Evaluation of CALCULATE modifiers

--      4. Application of filter arguments and KEEPFILTERS

DEFINE

    MEASURE Sales[Test] =

        AVERAGEX ( 

            VALUES ( 'Date'[Calendar Year] ),

            CALCULATE (

                [Sales Amount],

                'Product'[Category] = "Audio",

                KEEPFILTERS ( 'Product'[Color] IN { "Red", "Blue" } ),

                USERELATIONSHIP ( Sales[Delivery Date], 'Date'[Date] )

            )

        )

EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Color],

    "Sales Amount", [Test]

)

```

| Color | Sales Amount |
| --- | --- |
| Blue | 22,266.55 |
| Red | 16,561.91 |

## Related articles

Learn more about CALCULATE in the following articles:

- [**Context Transition and Filters in CALCULATE**](https://www.sqlbi.com/articles/context-transition-and-filters-in-calculate/)

  This article explains how the context transition interacts with the filter arguments of a CALCULATE function in DAX. This is important in order to avoid unexpected results with complex calculations made in filter arguments. [» Read more](https://www.sqlbi.com/articles/context-transition-and-filters-in-calculate/)
- [**Filter Arguments in CALCULATE**](https://www.sqlbi.com/articles/filter-arguments-in-calculate/)

  A filter argument in CALCULATE is always an iterator. Finding the right granularity for it is important to control the result and the performance. This article describes the options available to create complex filters in DAX. [» Read more](https://www.sqlbi.com/articles/filter-arguments-in-calculate/)
- [**Order of Evaluation in CALCULATE Parameters**](https://www.sqlbi.com/articles/order-of-evaluation-in-calculate-parameters/)

  DAX is the new language used by PowerPivot and Analysis Services in Tabular mode and it resembles the syntax of Excel formula and it can be considered a functional language. You do not have iterative statements, but you can run iterative functions like, for example, SUMX and FILTER. The most important functions in DAX are […] [» Read more](https://www.sqlbi.com/articles/order-of-evaluation-in-calculate-parameters/)
- [**Using OR conditions between slicers in DAX**](https://www.sqlbi.com/articles/using-or-conditions-between-slicers-in-dax/)

  This article describes how to implement in DAX a logical OR condition between the selection of two slicers of a Power BI report or of a PivotTable in Excel. By default, when relying on more than one slicer they are considered in an AND condition. [» Read more](https://www.sqlbi.com/articles/using-or-conditions-between-slicers-in-dax/)
- [**Context Transition and Expanded Tables**](https://www.sqlbi.com/articles/context-transition-and-expanded-tables/)

  This article describes how table expansion and filter context propagation are important DAX concepts to understand and fix small glitches in DAX expressions. [» Read more](https://www.sqlbi.com/articles/context-transition-and-expanded-tables/)
- [**Expanded tables in DAX**](https://www.sqlbi.com/articles/expanded-tables-in-dax/)

  Expanded tables are the core of DAX; understanding how they work is of paramount importance. This article provides a theoretical foundation of what expanded tables are, along with fundamental concepts useful when reading DAX code. [» Read more](https://www.sqlbi.com/articles/expanded-tables-in-dax/)
- [**Understanding context transition in DAX**](https://www.sqlbi.com/articles/understanding-context-transition-in-dax/)

  Context transition is one of the most obscure topics for DAX newbies. In this article we introduce context transition, its effects, and how to leverage it rather than be scared of it. [» Read more](https://www.sqlbi.com/articles/understanding-context-transition-in-dax/)
- [**Solving errors in CALCULATE filter arguments**](https://www.sqlbi.com/articles/solving-errors-in-calculate-filter-arguments/)

  The filter arguments in CALCULATE can be written as logical conditions with certain restrictions. This article explains the more common errors in these conditions and how to solve them. [» Read more](https://www.sqlbi.com/articles/solving-errors-in-calculate-filter-arguments/)
- [**Introducing horizontal fusion in DAX**](https://www.sqlbi.com/articles/introducing-horizontal-fusion-in-dax/)

  Horizontal fusion is a new optimization technique available in DAX to reduce the number of storage engine queries. In this article, we introduce this optimization with some examples. [» Read more](https://www.sqlbi.com/articles/introducing-horizontal-fusion-in-dax/)
- [**Introducing CALCULATE in DAX**](https://www.sqlbi.com/articles/introducing-calculate-in-dax/)

  CALCULATE is the most powerful and complex function in DAX. In this article, we provide an introduction to CALCULATE, its behavior, and how to use it. [» Read more](https://www.sqlbi.com/articles/introducing-calculate-in-dax/)
- [**Filter context in DAX**](https://www.sqlbi.com/articles/filter-context-in-dax/)

  Understanding the difference between a row context and a filter context is the first and most important concept to learn to use DAX correctly. This article introduces the filter context. [» Read more](https://www.sqlbi.com/articles/filter-context-in-dax/)
- [**Filter context in DAX explained visually**](https://www.sqlbi.com/articles/filter-context-in-dax-explained-visually/)

  This article describes the DAX filter context using a conceptual model based on a visual representation. [» Read more](https://www.sqlbi.com/articles/filter-context-in-dax-explained-visually/)
- [**Filter columns, not tables, in DAX**](https://www.sqlbi.com/articles/filter-columns-not-tables-in-dax/)

  One of the few golden rules in DAX is to always filter columns and never filter tables with CALCULATE. This article explains the rationale behind the rule. [» Read more](https://www.sqlbi.com/articles/filter-columns-not-tables-in-dax/)
- [**Context transition in DAX explained visually**](https://www.sqlbi.com/articles/context-transition-in-dax-explained-visually/)

  This article describes the DAX context transition using a conceptual model based on a visual representation. [» Read more](https://www.sqlbi.com/articles/context-transition-in-dax-explained-visually/)


