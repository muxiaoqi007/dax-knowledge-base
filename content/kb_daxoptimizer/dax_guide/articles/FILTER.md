---
title: "FILTER"
function: "filter"
category: "Filter"
url: "https://dax.guide/filter/"
source: "dax.guide"
重要度:
难度:
---

# FILTER DAX Function (Filter)

Returns a table that has been filtered.

## Syntax

FILTER ( <Table>, <FilterExpression> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | The table to be filtered. || FilterExpression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | A boolean (True/False) expression that is to be evaluated for each row of the table. |

## Return values

Table An entire table or a table with one or more columns.

A table containing only the filtered rows.

## Remarks

FILTER can filter rows from a table by using any expression valid in the row context.  
Thanks to context transition, using a measure in the filter expression it is possible to filter a table based on a dynamic calculation involving other rows and/or tables.

[» 9 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

Filter the customers in Europe.

```dax


EVALUATE

FILTER (

    Customer,

    Customer[Continent] = "Europe"

)

```

Use RELATED to access a column in a related table in a FILTER iterator. However, the usage of CALCULATE is preferred over FILTER, when possible.

```dax


--  Being an iterator, FILTER creates a row context. If you need

--  to access related tables, the RELATED function is needed.

--  This makes the usage of CALCULATE preferred over FILTER, when

--  possible

DEFINE

    MEASURE Sales[Red Sales] =

        SUMX (

            FILTER ( Sales, RELATED ( Product[Color] ) = "Red" ),

            Sales[Quantity] * Sales[Net Price]

        )

    MEASURE Sales[Red Sales CALCULATE] =

        CALCULATE ( [Sales Amount], KEEPFILTERS ( Product[Color] = "Red" ) )

EVALUATE

SUMMARIZECOLUMNS (

    Product[Brand],

    "Sales", [Sales Amount],

    "Red Sales", [Red Sales],

    "Red Sales CALCULATE", [Red Sales CALCULATE]

)

```

| Product[Brand] | Sales | Red Sales | Red Sales CALCULATE |
| --- | --- | --- | --- |
| Contoso | 7,352,399.03 | 579,062.70 | 579,062.70 |
| Wide World Importers | 1,901,956.66 | 41,435.95 | 41,435.95 |
| Northwind Traders | 1,040,552.13 | 9,187.51 | 9,187.51 |
| Adventure Works | 4,011,112.28 | 131,348.11 | 131,348.11 |
| Southridge Video | 1,384,413.85 | 24,362.90 | 24,362.90 |
| Litware | 3,255,704.03 | 113,120.32 | 113,120.32 |
| Fabrikam | 5,554,015.73 | 96,272.49 | 96,272.49 |
| Proseware | 2,546,144.16 | 108,667.41 | 108,667.41 |
| A. Datum | 2,096,184.64 | (Blank) | (Blank) |
| The Phone Company | 1,123,819.07 | (Blank) | (Blank) |
| Tailspin Toys | 325,042.42 | 6,644.72 | 6,644.72 |

FILTER is needed to create a filter based on a measure relying on context transition.

```dax


DEFINE

    MEASURE Sales[Sales Amount] =

        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

    MEASURE Sales[Sales in countries >3M] =

        CALCULATE (

            [Sales Amount],

            FILTER ( 

                ALL ( Customer[CountryRegion] ),

                [Sales Amount] > 3000000

            )

       )

EVALUATE

SUMMARIZECOLUMNS ( 

    'Date'[Calendar Year],

    "Sales amount", [Sales Amount],

    "Sales in countries >3M", [Sales in countries >3M] 

)

```

| Date[Calendar Year] | Sales amount | Sales in countries >3M |
| --- | --- | --- |
| 2007-01-01 | 11,309,946.12 | 7,209,663.88 |
| 2008-01-01 | 9,927,582.99 | 7,228,113.02 |
| 2009-01-01 | 9,353,814.87 | 3,103,916.04 |

FILTER is needed to iterate the content of a variable.

```dax


--  AVERAGE Monthly Sales computes the average sales for the months

--  with at least 10K sales

DEFINE

    MEASURE Sales[Sales Amount] =

        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

    MEASURE Sales[AVERAGE Monthly Sales] =

        VAR MonthlySales =

            ADDCOLUMNS (

                DISTINCT ( 'Date'[Calendar Year Month] ),

                "@MonthlySales", [Sales Amount]

            )

        VAR FilteredSales =

            -- Iterator required to filter the @MonthlySales column        

            FILTER ( MonthlySales, [@MonthlySales] > 10000 )

        VAR Result =

            AVERAGEX ( FilteredSales, [@MonthlySales] )

        RETURN

            Result

EVALUATE

SUMMARIZECOLUMNS ( 

    'Product'[Color], 

    "AVERAGE Monthly Sales", [AVERAGE Monthly Sales] 

)

```

| Color | AVERAGE Monthly Sales |
| --- | --- |
| Silver | 188,848.91 |
| Blue | 67,651.24 |
| White | 161,933.33 |
| Red | 32,219.43 |
| Black | 162,779.61 |
| Green | 42,147.07 |
| Orange | 30,672.37 |
| Pink | 26,985.05 |
| Grey | 97,476.06 |
| Silver Grey | 17,205.33 |
| Brown | 30,514.95 |
| Gold | 14,428.17 |
| Yellow | 12,524.31 |

## Related articles

Learn more about FILTER in the following articles:

- [**Filtering Tables in DAX**](https://www.sqlbi.com/articles/filtering-tables/)

  This article describes a number of techniques available to filter tables in DAX, showing possible pitfalls that you can avoid once you know them, in particular using bidirectional filters. One of the hardest things to do, when learning DAX, is to get rid of common sense reasoning and learn to follow a new set of […] [» Read more](https://www.sqlbi.com/articles/filtering-tables/)
- [**Context Transition and Filters in CALCULATE**](https://www.sqlbi.com/articles/context-transition-and-filters-in-calculate/)

  This article explains how the context transition interacts with the filter arguments of a CALCULATE function in DAX. This is important in order to avoid unexpected results with complex calculations made in filter arguments. [» Read more](https://www.sqlbi.com/articles/context-transition-and-filters-in-calculate/)
- [**Filter Arguments in CALCULATE**](https://www.sqlbi.com/articles/filter-arguments-in-calculate/)

  A filter argument in CALCULATE is always an iterator. Finding the right granularity for it is important to control the result and the performance. This article describes the options available to create complex filters in DAX. [» Read more](https://www.sqlbi.com/articles/filter-arguments-in-calculate/)
- [**FILTER vs CALCULATETABLE: optimization using cardinality estimation**](https://www.sqlbi.com/articles/filter-vs-calculatetable-optimization-using-cardinality-estimation/)

  A common best practice is to use CALCULATETABLE instead of FILTER for performance reasons. This article explores the reasons why and explains when FILTER might be better than CALCULATETABLE. [» Read more](https://www.sqlbi.com/articles/filter-vs-calculatetable-optimization-using-cardinality-estimation/)
- [**From SQL to DAX: Filtering Data**](https://www.sqlbi.com/articles/from-sql-to-dax-filtering-data/)

  The WHERE condition of an SQL statement has two counterparts in DAX: FILTER and CALCULATETABLE. In this article we explore the differences between them, providing a few best practices in their use. [» Read more](https://www.sqlbi.com/articles/from-sql-to-dax-filtering-data/)
- [**Applying a measure filter in Power BI**](https://www.sqlbi.com/articles/applying-a-measure-filter-in-power-bi/)

  This article describes how to use a measure to filter a Power BI visualization, and the different behaviors of a same filter between different visuals. [» Read more](https://www.sqlbi.com/articles/applying-a-measure-filter-in-power-bi/)
- [**Introducing CALCULATE in DAX**](https://www.sqlbi.com/articles/introducing-calculate-in-dax/)

  CALCULATE is the most powerful and complex function in DAX. In this article, we provide an introduction to CALCULATE, its behavior, and how to use it. [» Read more](https://www.sqlbi.com/articles/introducing-calculate-in-dax/)
- [**Row context in DAX**](https://www.sqlbi.com/articles/row-context-in-dax/)

  Understanding the difference between row context and filter context is the first and most important concept to learn to use DAX correctly. This article introduces the row context, and is part of a series of articles about evaluation contexts in DAX. [» Read more](https://www.sqlbi.com/articles/row-context-in-dax/)
- [**Filter columns, not tables, in DAX**](https://www.sqlbi.com/articles/filter-columns-not-tables-in-dax/)

  One of the few golden rules in DAX is to always filter columns and never filter tables with CALCULATE. This article explains the rationale behind the rule. [» Read more](https://www.sqlbi.com/articles/filter-columns-not-tables-in-dax/)

## Related functions

Other related functions are:

- [[CALCULATETABLE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/filter-function-dax](https://docs.microsoft.com/en-us/dax/filter-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
