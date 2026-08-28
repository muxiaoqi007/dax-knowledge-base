---
title: "INT"
function: "int"
category: "Math and Trig"
url: "https://dax.guide/int/"
source: "dax.guide"
重要度:
难度:
---

# INT DAX Function (Math and Trig)

Rounds a number down to the nearest integer.

## Syntax

INT ( <Number> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | The number you want to round down to an integer. |

## Return values

Scalar A single value of any type.

An integer value of [Integer](https://dax.guide/dt/integer/) or [Currency](https://dax.guide/dt/currency/) data type, depending on the argument data type.

## Remarks

INT performs a type conversion to an integer and always returns an integer value.  
If <Number> is an [Integer](https://dax.guide/dt/integer/) or a [Decimal](https://dax.guide/dt/decimal/) data type data type, then the result is an [Integer](https://dax.guide/dt/integer/) data type.  
If <Number> is a [Currency](https://dax.guide/dt/currency/), then the result is also a [Currency](https://dax.guide/dt/currency/) data type.  
Other rounding functions (such as [[TRUNC]]) return a [Decimal](https://dax.guide/dt/decimal/) data type.

INT rounds <Number> towards −∞ to the nearest integer. If <Number> is already an integer, no rounding occurs. This operation is the same as taking the integer part of <Number>, but only when <Number> is non-negative.

[» 2 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

The following examples clarify the behavior of INT with negative and positive numbers.

```dax


INT ( - 2.9 ) = -3

INT ( - 2.1 ) = -3

INT ( - 0.9 ) = -1

INT ( - 0.1 ) = -1

INT ( 0.1 ) = 0

INT ( 0.9 ) = 0

INT ( 2.1 ) = 2

INT ( 2.9 ) = 2

```

```dax


--  INT converts any expression in an integer

EVALUATE 

    {

        ( "Integer 1", INT ( 10 ) ),

        ( "Integer 2", INT ( TRUE ) ),

        ( "Integer 3", INT ( "1950.00" ) ),

        ( "Integer 4", INT ( 190.89876 ) ),

        ( "Integer 5", INT ( "1E6" ) )

    }

```

| Value1 | Value2 |
| --- | --- |
| 2001-01-01 | 10 |
| 2001-02-01 | 1 |
| 2001-03-01 | 1,950 |
| 2001-04-01 | 190 |
| 2001-05-01 | 1,000,000 |

## Related articles

Learn more about INT in the following articles:

- [**Choosing Numeric Data Types in DAX**](https://www.sqlbi.com/articles/choosing-numeric-data-types-in-dax/)

  A data model for DAX has three numeric data types: integer, floating point, and fixed decimal number. This article describes them and explains why the fixed decimal number should be used instead of the floating point in most scenarios. [» Read more](https://www.sqlbi.com/articles/choosing-numeric-data-types-in-dax/)
- [**Differences between INT and CONVERT in DAX**](https://www.sqlbi.com/articles/differences-between-int-and-convert-in-dax/)

  This article describes the small differences between INT and CONVERT in DAX that may end up returning different results in arithmetic expressions. [» Read more](https://www.sqlbi.com/articles/differences-between-int-and-convert-in-dax/)

## Related functions

Other related functions are:

- [[CURRENCY]]
- [[TRUNC]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/int-function-dax](https://docs.microsoft.com/en-us/dax/int-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
