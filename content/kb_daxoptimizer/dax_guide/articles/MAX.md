---
title: "MAX"
function: "max"
category: "Aggregation"
url: "https://dax.guide/max/"
source: "dax.guide"
重要度:
难度:
---

# MAX DAX Function (Aggregation)

Returns the largest value in a column, or the larger value between two scalar expressions. Ignores logical values. Strings are compared according to alphabetical order.

## Syntax

MAX ( <ColumnNameOrScalar1> [, <Scalar2>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnNameOrScalar1 |  | The column in which you want to find the largest value, or the first scalar expression to compare. |
| Scalar2 | Optional | The second value to compare. |

## Return values

Scalar A single value of any type.

Largest value found in the column or in the two expressions.

## Remarks

When used with a single column, the MAX function internally executes [[MAXX]], without any performance difference.  
The following MAX call:

```dax


MAX ( table[column] )

```

corresponds to the following [[MAXX]] call:

```dax


MAXX (

    table,

    table[column] 

)

```

The result is blank in case there are no rows in the table with a non-blank value.

When used with two arguments, the syntax:

```dax


MAX ( exp1, exp2 )

```

corresponds to:

```dax


VAR v1 = exp1

VAR v2 = exp2

RETURN 

    SWITCH ( TRUE,

        v1 = v2, IF ( ISBLANK ( v1 ), v2, v1 ),

        v1 > v2, v1,

        v2 

    )

```

Thus, when used with two arguments, MAX consider the [[BLANK]] value as significative, whereas [[MAXX]] ignores it.

```dax


-- MAX returns BLANK

MAX ( -2, BLANK() ) 



-- MAXX returns -2

MAXX ( { -2, BLANK() }, [Value] ) 

```

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


--  MAX can be used with two scalars, in that case it 

--  returns the maximum between its arguments 

DEFINE

    MEASURE Sales[Sales Amount] =

        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

    MEASURE Sales[10K or Sales Amount] = 

        MAX ( 10000, [Sales Amount] )

EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Color],

    "Sales Amount", [Sales Amount],

    "10K or Sales Amount", [10K or Sales Amount]

)

ORDER BY [Sales Amount] ASC

```

| Color | Sales Amount | 10K or Sales Amount |
| --- | --- | --- |
| Transparent | 3,295.89 | 10,000.00 |
| Purple | 5,973.84 | 10,000.00 |
| Yellow | 89,715.56 | 89,715.56 |
| Azure | 97,389.89 | 97,389.89 |
| Gold | 361,496.01 | 361,496.01 |
| Silver Grey | 371,908.92 | 371,908.92 |
| Pink | 828,638.54 | 828,638.54 |
| Orange | 857,320.28 | 857,320.28 |
| Brown | 1,029,508.95 | 1,029,508.95 |
| Red | 1,110,102.10 | 1,110,102.10 |
| Green | 1,403,184.38 | 1,403,184.38 |
| Blue | 2,435,444.62 | 2,435,444.62 |
| Grey | 3,509,138.09 | 3,509,138.09 |
| White | 5,829,599.91 | 5,829,599.91 |
| Black | 5,860,066.14 | 5,860,066.14 |
| Silver | 6,798,560.86 | 6,798,560.86 |

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

Learn more about MAX in the following articles:

- [**Computing running totals in DAX**](https://www.sqlbi.com/articles/computing-running-totals-in-dax/)

  This article shows how to compute a running total over a dimension, like for example the date. [» Read more](https://www.sqlbi.com/articles/computing-running-totals-in-dax/)
- [**Understanding the difference between LASTDATE and MAX in DAX**](https://www.sqlbi.com/articles/understanding-the-difference-between-lastdate-and-max-in-dax/)

  This article explains why in many cases, MAX should be used instead of LASTDATE to search for the last date in a time period using DAX. [» Read more](https://www.sqlbi.com/articles/understanding-the-difference-between-lastdate-and-max-in-dax/)
- [**Computing MTD, QTD, YTD in Power BI for the current period**](https://www.sqlbi.com/articles/computing-mtd-qtd-ytd-in-power-bi-for-the-current-period/)

  This article describes how to use the DAX time intelligence calculations applied to the latest period available in the data, also known as the “current” period. [» Read more](https://www.sqlbi.com/articles/computing-mtd-qtd-ytd-in-power-bi-for-the-current-period/)

## Related functions

Other related functions are:

- [[MAXX]]
- [[MAXA]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/max-function-dax](https://docs.microsoft.com/en-us/dax/max-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
