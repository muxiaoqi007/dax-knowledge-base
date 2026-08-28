---
title: "AVERAGE"
function: "average"
category: "Aggregation"
url: "https://dax.guide/average/"
source: "dax.guide"
重要度:
难度:
---

# AVERAGE DAX Function (Aggregation)

Returns the average (arithmetic mean) of all the numbers in a column.

## Syntax

AVERAGE ( <ColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName |  | The column that contains the numbers for which you want the average. |

## Return values

Scalar A single value of one these types: [currency](https://dax.guide/dt/currency), [decimal](https://dax.guide/dt/decimal).

## Remarks

The AVERAGE function internally executes [[AVERAGEX]], without any performance difference.  
The following AVERAGE call:

```dax


AVERAGE ( table[column] )

```

corresponds to the following [[AVERAGEX]] call:

```dax


AVERAGEX (

    table,

    table[column]

)

```

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

More examples available for the AVERAGEX function.

## Related articles

Learn more about AVERAGE in the following articles:

- [**Using tuple syntax in DAX expressions**](https://www.sqlbi.com/articles/using-tuple-syntax-in-dax-expressions/)

  This article describes the use of the tuple syntax in DAX expressions to simplify comparisons involving two or more columns. [» Read more](https://www.sqlbi.com/articles/using-tuple-syntax-in-dax-expressions/)
- [**Introducing EXPAND and COLLAPSE for visual calculations in Power BI**](https://www.sqlbi.com/articles/introducing-expand-and-collapse-for-visual-calculations/)

  This article introduces the two basic visual context navigation functions: EXPAND and COLLAPSE. [» Read more](https://www.sqlbi.com/articles/introducing-expand-and-collapse-for-visual-calculations/)

## Related functions

Other related functions are:

- [[AVERAGEA]]
- [[AVERAGEX]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/average-function-dax](https://docs.microsoft.com/en-us/dax/average-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
