---
title: "AVERAGEX"
function: "averagex"
category: "Aggregation"
url: "https://dax.guide/averagex/"
source: "dax.guide"
重要度:
难度:
---

# AVERAGEX DAX Function (Aggregation)

Calculates the average (arithmetic mean) of a set of expressions evaluated over a table.

## Syntax

AVERAGEX ( <Table>, <Expression> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | The table containing the rows for which the expression will be evaluated. || Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | The expression to be evaluated for each row of the table. |

## Return values

Scalar A single value of one these types: [currency](https://dax.guide/dt/currency), [decimal](https://dax.guide/dt/decimal).

## Remarks

Blanks values returned by the expression are ignored for the average.

[» 2 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  AVERAGE is the short version of AVERAGEX, when used with one column only

--  In DAX, there are no differences between AVERAGEA and AVERAGE

DEFINE

    MEASURE Sales[AVG Quantity 1] = AVERAGE ( Sales[Quantity] )

    MEASURE Sales[AVG Quantity 2] = AVERAGEX ( Sales, Sales[Quantity] )

    MEASURE Sales[AVG Line Amount] =

        AVERAGEX ( Sales, Sales[Quantity] * Sales[Net Price] )

EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Color],

    "AVG Quantity 1", [AVG Quantity 1],

    "AVG Quantity 2", [AVG Quantity 2],

    "AVG Line Amount", [AVG Line Amount]

)

```

| Color | AVG Quantity 1 | AVG Quantity 2 | AVG Line Amount |
| --- | --- | --- | --- |
| Silver | 1.40 | 1.40 | 344.49 |
| Blue | 1.41 | 1.41 | 388.00 |
| White | 1.40 | 1.40 | 266.75 |
| Red | 1.39 | 1.39 | 191.33 |
| Black | 1.40 | 1.40 | 243.68 |
| Green | 1.40 | 1.40 | 652.64 |
| Orange | 1.40 | 1.40 | 543.64 |
| Pink | 1.40 | 1.40 | 235.54 |
| Yellow | 1.42 | 1.42 | 47.90 |
| Purple | 1.36 | 1.36 | 79.65 |
| Brown | 1.40 | 1.40 | 559.52 |
| Grey | 1.40 | 1.40 | 411.63 |
| Gold | 1.41 | 1.41 | 365.89 |
| Azure | 1.37 | 1.37 | 244.70 |
| Silver Grey | 1.42 | 1.42 | 550.98 |
| Transparent | 1.40 | 1.40 | 3.68 |

```dax


--  AVERAGEX is required when you need to iterate over

--  a table an average the result of a measure

DEFINE

    MEASURE Sales[Sales Amount] = SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

    MEASURE Sales[AVG Customer] = AVERAGEX ( Customer, [Sales Amount] )

EVALUATE

SUMMARIZECOLUMNS (

    'Customer'[Continent],

    "AVG Customer", [AVG Customer]

)

```

| Continent | AVG Customer |
| --- | --- |
| Asia | 4,972.51 |
| North America | 2,223.02 |
| Europe | 2,253.27 |

```dax


--  AVERAGEX is needed to iterate the content of a variable

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

            FILTER ( MonthlySales, [@MonthlySales] > 10000 )

        VAR Result =

            -- Iterator required to aggregate the @MonthlySales column        

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

```dax


--  AVERAGEX ignores blanks, it considers zeroes

EVALUATE

    VAR ValsWithBlank = { 1, 2, 3, BLANK () }

    VAR ValsWithZero  = { 1, 2, 3, 0        }

RETURN

    {

        ( "Average with BLANK", AVERAGEX ( ValsWithBlank, [Value] ) ),

        ( "Average with zero",  AVERAGEX ( ValsWithZero,  [Value] ) )

    }

```

| Value1 | Value2 |
| --- | --- |
| Average with BLANK | 2.00 |
| Average with zero | 1.50 |

## Related articles

Learn more about AVERAGEX in the following articles:

- [**Why Power BI totals might seem inaccurate**](https://www.sqlbi.com/articles/why-power-bi-totals-might-seem-inaccurate/)

  A common question is why Power BI totals are inaccurate because they do not display the sum of individual rows. In this article, we explain the reasons why those totals are correct. [» Read more](https://www.sqlbi.com/articles/why-power-bi-totals-might-seem-inaccurate/)
- [**Using visual calculations for conditional formatting**](https://www.sqlbi.com/articles/using-visual-calculations-for-conditional-formatting/)

  Visual calculations are useful for performing calculations specific to a visual. Conditional formatting is a great example of this concept. In this article, we show how to easily implement conditional formatting through visual calculations. [» Read more](https://www.sqlbi.com/articles/using-visual-calculations-for-conditional-formatting/)

## Related functions

Other related functions are:

- [[AVERAGE]]
- [[AVERAGEA]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/averagex-function-dax](https://docs.microsoft.com/en-us/dax/averagex-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
