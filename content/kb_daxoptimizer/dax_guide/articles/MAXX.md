---
title: "MAXX"
function: "maxx"
category: "Aggregation"
url: "https://dax.guide/maxx/"
source: "dax.guide"
重要度:
难度:
---

# MAXX DAX Function (Aggregation)

Returns the largest value that results from evaluating an expression for each row of a table. Strings are compared according to alphabetical order.

## Syntax

MAXX ( <Table>, <Expression> [, <Variant>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | The table containing the rows for which the expression will be evaluated. || Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | The expression to be evaluated for each row of the table. |
| Variant | Optional | If true, maximum value in col will be same as order by variant desc column. Default is false. |

## Return values

Scalar A single value of any type.

Largest value found in the expression.

[» 3 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  MAX is the short version of MAXX, when used with one column only

DEFINE

    MEASURE Sales[MAX Net Price 1] = MAX ( Sales[Net Price] )

    MEASURE Sales[MAX Net Price 2] = MAXX ( Sales, Sales[Net Price] )

    MEASURE Sales[MAX Line Amount] = 

        MAXX ( Sales, Sales[Quantity] * Sales[Net Price] )

EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Color],

    "MAX Net Price 1", [MAX Net Price 1],

    "MAX Net Price 2", [MAX Net Price 2],

    "MAX Line Amount", [MAX Line Amount]

)

```

| Color | MAX Net Price 1 | MAX Net Price 2 | MAX Line Amount |
| --- | --- | --- | --- |
| Silver | 2,879.99 | 2,879.99 | 9,996.00 |
| Blue | 3,199.99 | 3,199.99 | 12,799.96 |
| White | 3,199.99 | 3,199.99 | 12,799.96 |
| Red | 1,989.00 | 1,989.00 | 7,956.00 |
| Black | 2,499.00 | 2,499.00 | 9,996.00 |
| Green | 3,199.99 | 3,199.99 | 10,239.97 |
| Orange | 2,879.99 | 2,879.99 | 8,639.97 |
| Pink | 1,989.00 | 1,989.00 | 7,956.00 |
| Yellow | 789.75 | 789.75 | 2,369.25 |
| Purple | 104.89 | 104.89 | 419.56 |
| Brown | 3,199.99 | 3,199.99 | 12,799.96 |
| Grey | 3,199.99 | 3,199.99 | 12,799.96 |
| Gold | 605.70 | 605.70 | 2,356.00 |
| Azure | 290.00 | 290.00 | 1,160.00 |
| Silver Grey | 673.00 | 673.00 | 2,692.00 |
| Transparent | 2.94 | 2.94 | 11.76 |

```dax


--  MAXX is needed to iterate the content of a variable,

--  indeed MAX works only with columns in the model

DEFINE

    MEASURE Sales[Sales Amount] =

        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

    MEASURE Sales[MAX Monthly Sales] =

        VAR MonthlySales =

            ADDCOLUMNS (

                DISTINCT ( 'Date'[Calendar Year Month] ),

                "@MonthlySales", [Sales Amount]

            )

        VAR FilteredSales =

            FILTER ( MonthlySales, [@MonthlySales] > 10000 )

        VAR Result =

            -- Iterator required to aggregate the @MonthlySales column        

            MAXX ( FilteredSales, [@MonthlySales] )

        RETURN

            Result

EVALUATE

SUMMARIZECOLUMNS ( 

    'Product'[Color], 

    "MAX Monthly Sales", [MAX Monthly Sales] 

)

```

| Color | MAX Monthly Sales |
| --- | --- |
| Silver | 355,007.95 |
| Blue | 161,351.67 |
| White | 333,756.67 |
| Red | 77,069.70 |
| Black | 293,091.79 |
| Green | 93,839.92 |
| Orange | 69,139.50 |
| Pink | 86,940.06 |
| Grey | 169,268.89 |
| Silver Grey | 31,808.00 |
| Brown | 101,388.85 |
| Gold | 20,951.00 |
| Yellow | 13,791.93 |

## Related articles

Learn more about MAXX in the following articles:

- [**Highlighting the minimum and maximum values in a Power BI matrix**](https://www.sqlbi.com/articles/highlighting-the-minimum-and-maximum-values-in-a-power-bi-matrix/)

  This article shows how to use DAX and conditional formatting together to highlight the minimum and maximum values in a matrix in Power BI. [» Read more](https://www.sqlbi.com/articles/highlighting-the-minimum-and-maximum-values-in-a-power-bi-matrix/)
- [**Computing MTD, QTD, YTD in Power BI for the current period**](https://www.sqlbi.com/articles/computing-mtd-qtd-ytd-in-power-bi-for-the-current-period/)

  This article describes how to use the DAX time intelligence calculations applied to the latest period available in the data, also known as the “current” period. [» Read more](https://www.sqlbi.com/articles/computing-mtd-qtd-ytd-in-power-bi-for-the-current-period/)
- [**Using DAX to control a chart range in Power BI**](https://www.sqlbi.com/articles/using-dax-to-control-a-chart-range-in-power-bi/)

  You can rely on DAX expressions to control the behavior of some aspects of Power BI visuals. This article shows a few examples of managing the range of charts. [» Read more](https://www.sqlbi.com/articles/using-dax-to-control-a-chart-range-in-power-bi/)

## Related functions

Other related functions are:

- [[MAX]]
- [[MAXA]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/maxx-function-dax](https://docs.microsoft.com/en-us/dax/maxx-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
