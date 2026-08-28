---
title: "MINX"
function: "minx"
category: "Aggregation"
url: "https://dax.guide/minx/"
source: "dax.guide"
重要度:
难度:
---

# MINX DAX Function (Aggregation)

Returns the smallest value that results from evaluating an expression for each row of a table. Strings are compared according to alphabetical order.

## Syntax

MINX ( <Table>, <Expression> [, <Variant>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | The table containing the rows for which the expression will be evaluated. || Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | The expression to be evaluated for each row of the table. |
| Variant | Optional | If true, minimum value in col will be same as order by variant asc column. Default is false. |

## Return values

Scalar A single value of any type.

Smallest value found in the expression.

[» 1 related article](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  MIN is the short version of MINX, when used with one column only

DEFINE

    MEASURE Sales[MIN Net Price 1] = MIN ( Sales[Net Price] )

    MEASURE Sales[MIN Net Price 2] = MINX ( Sales, Sales[Net Price] )

    MEASURE Sales[MIN Line Amount] =

        MINX ( Sales, Sales[Quantity] * Sales[Net Price] )

EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Color],

    "MIN Net Price 1", [MIN Net Price 1],

    "MIN Net Price 2", [MIN Net Price 2],

    "MIN Line Amount", [MIN Line Amount]

)

```

| Color | MIN Net Price 1 | MIN Net Price 2 | MIN Line Amount |
| --- | --- | --- | --- |
| Silver | 0.76 | 0.76 | 0.76 |
| Blue | 8.99 | 8.99 | 8.99 |
| White | 1.79 | 1.79 | 1.79 |
| Red | 3.98 | 3.98 | 3.98 |
| Black | 0.88 | 0.88 | 0.88 |
| Green | 3.99 | 3.99 | 3.99 |
| Orange | 23.96 | 23.96 | 23.96 |
| Pink | 0.76 | 0.76 | 0.76 |
| Yellow | 3.99 | 3.99 | 3.99 |
| Purple | 22.40 | 22.40 | 22.40 |
| Brown | 23.99 | 23.99 | 23.99 |
| Grey | 0.95 | 0.95 | 0.95 |
| Gold | 13.90 | 13.90 | 13.90 |
| Azure | 103.20 | 103.20 | 103.20 |
| Silver Grey | 158.40 | 158.40 | 158.40 |
| Transparent | 2.35 | 2.35 | 2.35 |

```dax


--  MINX is needed to iterate the content of a variable,

--  indeed MIN works only with columns in the model

DEFINE

    MEASURE Sales[Sales Amount] =

        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

    MEASURE Sales[MIN Monthly Sales] =

        VAR MonthlySales =

            ADDCOLUMNS (

                DISTINCT ( 'Date'[Calendar Year Month] ),

                "@MonthlySales", [Sales Amount]

            )

        VAR FilteredSales =

            FILTER ( MonthlySales, [@MonthlySales] > 10000 )

        VAR Result =

            -- Iterator required to aggregate the @MonthlySales column        

            MINX ( FilteredSales, [@MonthlySales] )

        RETURN

            Result

EVALUATE

SUMMARIZECOLUMNS ( 

    'Product'[Color], 

    "MINX Monthly Sales", [MIN Monthly Sales] 

)

```

| Color | MINX Monthly Sales |
| --- | --- |
| Silver | 64,226.40 |
| Blue | 21,907.71 |
| White | 65,678.96 |
| Red | 11,202.40 |
| Black | 94,310.36 |
| Green | 13,555.70 |
| Orange | 11,399.62 |
| Pink | 10,333.59 |
| Grey | 28,463.19 |
| Silver Grey | 10,565.60 |
| Brown | 10,972.75 |
| Gold | 10,056.00 |
| Yellow | 11,256.69 |

## Related articles

Learn more about MINX in the following articles:

- [**Highlighting the minimum and maximum values in a Power BI matrix**](https://www.sqlbi.com/articles/highlighting-the-minimum-and-maximum-values-in-a-power-bi-matrix/)

  This article shows how to use DAX and conditional formatting together to highlight the minimum and maximum values in a matrix in Power BI. [» Read more](https://www.sqlbi.com/articles/highlighting-the-minimum-and-maximum-values-in-a-power-bi-matrix/)

## Related functions

Other related functions are:

- [[MIN]]
- [[MINA]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/minx-function-dax](https://docs.microsoft.com/en-us/dax/minx-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
