---
title: "SUMX"
function: "sumx"
category: "Aggregation"
url: "https://dax.guide/sumx/"
source: "dax.guide"
重要度:
难度:
---

# SUMX DAX Function (Aggregation)

Returns the sum of an expression evaluated for each row in a table.

## Syntax

SUMX ( <Table>, <Expression> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | The table containing the rows for which the expression will be evaluated. || Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | The expression to be evaluated for each row of the table. |

## Return values

Scalar A single value of any type.

Result of the sum.

[» 4 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  SUM is the short version of SUMX, when used with one column only

--  SUMX is required to evaluate formulas, instead of columns

DEFINE

    MEASURE Sales[# Quantity 1] = SUM ( Sales[Quantity] )

    MEASURE Sales[# Quantity 2] = SUMX ( Sales, Sales[Quantity] )

    MEASURE Sales[Sales Amount] =

        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Color],

    "Quantity 1", [# Quantity 1],

    "Quantity 2", [# Quantity 2],

    "Sales Amount", [Sales Amount]

)

```

| Color | Quantity 1 | Quantity 2 | Sales Amount |
| --- | --- | --- | --- |
| Silver | 27,551 | 27,551 | 6,798,560.86 |
| Blue | 8,859 | 8,859 | 2,435,444.62 |
| White | 30,543 | 30,543 | 5,829,599.91 |
| Red | 8,079 | 8,079 | 1,110,102.10 |
| Black | 33,618 | 33,618 | 5,860,066.14 |
| Green | 3,020 | 3,020 | 1,403,184.38 |
| Orange | 2,203 | 2,203 | 857,320.28 |
| Pink | 4,921 | 4,921 | 828,638.54 |
| Yellow | 2,665 | 2,665 | 89,715.56 |
| Purple | 102 | 102 | 5,973.84 |
| Brown | 2,570 | 2,570 | 1,029,508.95 |
| Grey | 11,900 | 11,900 | 3,509,138.09 |
| Gold | 1,393 | 1,393 | 361,496.01 |
| Azure | 546 | 546 | 97,389.89 |
| Silver Grey | 959 | 959 | 371,908.92 |
| Transparent | 1,251 | 1,251 | 3,295.89 |

```dax


--  SUMX is needed to iterate the content of a variable,

--  indeed SUM works only with columns in the model

DEFINE

    MEASURE Sales[Sales Amount] =

        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

    MEASURE Sales[SUM Monthly Sales] =

        VAR MonthlySales =

            ADDCOLUMNS (

                DISTINCT ( 'Date'[Calendar Year Month] ),

                "@MonthlySales", [Sales Amount]

            )

        VAR FilteredSales =

            FILTER ( MonthlySales, [@MonthlySales] > 10000 )

        VAR Result =

            -- Iterator required to aggregate the @MonthlySales column        

            SUMX ( FilteredSales, [@MonthlySales] )

        RETURN

            Result

EVALUATE

SUMMARIZECOLUMNS ( 

    'Product'[Color], 

    "SUM Monthly Sales", [SUM Monthly Sales] 

)

```

| Color | SUM Monthly Sales |
| --- | --- |
| Silver | 6,798,560.86 |
| Blue | 2,435,444.62 |
| White | 5,829,599.91 |
| Red | 1,095,460.51 |
| Black | 5,860,066.14 |
| Green | 1,390,853.24 |
| Orange | 797,481.72 |
| Pink | 782,566.58 |
| Grey | 3,509,138.09 |
| Silver Grey | 275,285.20 |
| Brown | 1,006,993.45 |
| Gold | 245,278.91 |
| Yellow | 25,048.62 |

## Related articles

Learn more about SUMX in the following articles:

- [**Optimizing nested iterators in DAX**](https://www.sqlbi.com/articles/optimizing-nested-iterators-in-dax/)

  This article describes possible optimization approaches to improve the performance of nested iterators in DAX. [» Read more](https://www.sqlbi.com/articles/optimizing-nested-iterators-in-dax/)
- [**Optimizing callbacks in a SUMX iterator**](https://www.sqlbi.com/articles/optimizing-callbacks-in-a-sumx-iterator/)

  This article explains a typical pattern to optimize a SUMX iterator by reducing the number of callbacks in the expression. [» Read more](https://www.sqlbi.com/articles/optimizing-callbacks-in-a-sumx-iterator/)
- [**Introducing horizontal fusion in DAX**](https://www.sqlbi.com/articles/introducing-horizontal-fusion-in-dax/)

  Horizontal fusion is a new optimization technique available in DAX to reduce the number of storage engine queries. In this article, we introduce this optimization with some examples. [» Read more](https://www.sqlbi.com/articles/introducing-horizontal-fusion-in-dax/)
- [**Row context in DAX**](https://www.sqlbi.com/articles/row-context-in-dax/)

  Understanding the difference between row context and filter context is the first and most important concept to learn to use DAX correctly. This article introduces the row context, and is part of a series of articles about evaluation contexts in DAX. [» Read more](https://www.sqlbi.com/articles/row-context-in-dax/)

## Related functions

Other related functions are:

- [[SUM]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/sumx-function-dax](https://docs.microsoft.com/en-us/dax/sumx-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
