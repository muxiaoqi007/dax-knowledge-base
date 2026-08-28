---
title: "CONVERT"
function: "convert"
category: "Math and Trig"
url: "https://dax.guide/convert/"
source: "dax.guide"
重要度:
难度:
---

# CONVERT DAX Function (Math and Trig)

Convert an expression to the specified data type.

## Syntax

CONVERT ( <Expression>, <DataType> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Expression |  | An expression which needs to be converted. |
| DataType |  | An enumeration that includes: BOOLEAN/LOGICAL, CURRENCY/DECIMAL, DATETIME, DOUBLE, INTEGER/INT64, STRING/TEXT. |

## Return values

Scalar A single value of any type.

The value of the Expression converted to the desired DataType.

[» 3 related articles](#articles)  

## Examples

```dax


--  CONVERT converts any expression in the requested datatype

--  Supported data types are: 

--  INTEGER, DOUBLE, STRING, BOOLEAN, CURRENCY, DATETIME

EVALUATE 

    {

        ( "Date1", CONVERT ( "12/25/1966", DATETIME ) ),

        ( "Date2", CONVERT ( "12-25-1966", DATETIME ) ),

        ( "Date3", CONVERT ( 12345,        DATETIME ) ),

        ( "Date4", CONVERT ( FALSE,        DATETIME ) )

    }

```

| Value1 | Value2 |
| --- | --- |
| Date1 | 1966-12-25 00:00:00 |
| Date2 | 1966-12-25 00:00:00 |
| Date3 | 1933-10-18 00:00:00 |
| Date4 | 1899-12-30 00:00:00 |

## Related articles

Learn more about CONVERT in the following articles:

- [**Differences between INT and CONVERT in DAX**](https://www.sqlbi.com/articles/differences-between-int-and-convert-in-dax/)

  This article describes the small differences between INT and CONVERT in DAX that may end up returning different results in arithmetic expressions. [» Read more](https://www.sqlbi.com/articles/differences-between-int-and-convert-in-dax/)
- [**Replacing relationships with join functions in DAX**](https://www.sqlbi.com/articles/replacing-relationships-with-join-functions-in-dax/)

  This article describes how to join tables in DAX when there are no relationships in the data model. The data lineage plays an essential role in this scenario. [» Read more](https://www.sqlbi.com/articles/replacing-relationships-with-join-functions-in-dax/)
- [**Rounding errors with different data types in DAX**](https://www.sqlbi.com/articles/rounding-errors-with-different-data-types-in-dax/)

  This article describes the possible rounding differences that can appear in DAX. They are related to the data types and the operation being performed: knowing these details helps you write more robust DAX formulas and avoid errors in comparisons. [» Read more](https://www.sqlbi.com/articles/rounding-errors-with-different-data-types-in-dax/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/convert-function-dax](https://docs.microsoft.com/en-us/dax/convert-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
