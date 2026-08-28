---
title: "MIN"
function: "min"
category: "Aggregation"
url: "https://dax.guide/min/"
source: "dax.guide"
重要度:
难度:
---

# MIN DAX Function (Aggregation)

Returns the smallest value in a column, or the smaller value between two scalar expressions. Ignores logical values. Strings are compared according to alphabetical order.

## Syntax

MIN ( <ColumnNameOrScalar1> [, <Scalar2>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnNameOrScalar1 |  | The column in which you want to find the smallest value, or the first scalar expression to compare. |
| Scalar2 | Optional | The second value to compare. |

## Return values

Scalar A single value of any type.

Smallest value found in the column or in the two expressions.

## Remarks

When used with a single column, the MIN function internally executes [[MINX]], without any performance difference.  
The following MIN call:

```dax


MIN ( table[column] )

```

corresponds to the following [[MINX]] call:

```dax


MINX (

    table,

    table[column] 

)

```

The result is blank in case there are no rows in the table with a non-blank value.

When used with two arguments, the syntax:

```dax


MIN ( exp1, exp2 )

```

corresponds to:

```dax


VAR v1 = exp1

VAR v2 = exp2

RETURN 

    SWITCH ( TRUE,

        v1 = v2, IF ( ISBLANK ( v1 ), v2, v1 ),

        v1 < v2, v1,

        v2 

    )

```

Thus, when used with two arguments, MIN consider the [[BLANK]] value as significative, whereas [[MINX]] ignores it.

```dax


-- MIN returns BLANK

MIN ( 2, BLANK() ) 



-- MINX returns 2

MINX ( { 2, BLANK() }, [Value] ) 

```

[» 2 related articles](#articles)  
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


--  MIN can be used with two scalars, in that case it 

--  returns the minimum between its arguments

DEFINE

    MEASURE Sales[Sales Amount] =

        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

    MEASURE Sales[5M or Sales Amount] = 

        MIN ( 5000000, [Sales Amount] )

EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Color],

    "Sales Amount", [Sales Amount],

    "5M or Sales Amount", [5M or Sales Amount]

)

ORDER BY [Color] 

```

| Color | Sales Amount | 5M or Sales Amount |
| --- | --- | --- |
| Azure | 97,389.89 | 97,389.89 |
| Black | 5,860,066.14 | 5,000,000.00 |
| Blue | 2,435,444.62 | 2,435,444.62 |
| Brown | 1,029,508.95 | 1,029,508.95 |
| Gold | 361,496.01 | 361,496.01 |
| Green | 1,403,184.38 | 1,403,184.38 |
| Grey | 3,509,138.09 | 3,509,138.09 |
| Orange | 857,320.28 | 857,320.28 |
| Pink | 828,638.54 | 828,638.54 |
| Purple | 5,973.84 | 5,973.84 |
| Red | 1,110,102.10 | 1,110,102.10 |
| Silver | 6,798,560.86 | 5,000,000.00 |
| Silver Grey | 371,908.92 | 371,908.92 |
| Transparent | 3,295.89 | 3,295.89 |
| White | 5,829,599.91 | 5,000,000.00 |
| Yellow | 89,715.56 | 89,715.56 |

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

Learn more about MIN in the following articles:

- [**Understanding the difference between LASTDATE and MAX in DAX**](https://www.sqlbi.com/articles/understanding-the-difference-between-lastdate-and-max-in-dax/)

  This article explains why in many cases, MAX should be used instead of LASTDATE to search for the last date in a time period using DAX. [» Read more](https://www.sqlbi.com/articles/understanding-the-difference-between-lastdate-and-max-in-dax/)
- [**Optional parameters in DAX user-defined functions**](https://www.sqlbi.com/articles/optional-parameters-in-dax-user-defined-functions/)

  This article describes how to define optional parameters in DAX user-defined functions and set default values for parameters not specified by the caller. [» Read more](https://www.sqlbi.com/articles/optional-parameters-in-dax-user-defined-functions/)

## Related functions

Other related functions are:

- [[MINA]]
- [[MINX]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/min-function-dax](https://docs.microsoft.com/en-us/dax/min-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
