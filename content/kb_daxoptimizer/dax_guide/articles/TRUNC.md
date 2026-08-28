---
title: "TRUNC"
function: "trunc"
category: "Math and Trig"
url: "https://dax.guide/trunc/"
source: "dax.guide"
重要度:
难度:
---

# TRUNC DAX Function (Math and Trig)

Truncates a number to an integer by removing the decimal, or fractional, part of the number.

## Syntax

TRUNC ( <Number> [, <NumberOfDigits>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | The number you want to truncate. |
| NumberOfDigits | Optional | A number specifying the precision of the truncation; if omitted, 0 (zero). |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

The truncated number.

## Remarks

If NumberOfDigits is greater than 0 (zero), then number is truncated to the specified number of decimal places.

If NumberOfDigits is 0, the number is truncated to the integer part.

If NumberOfDigits is less than 0, the number is truncated to the left of the decimal point.

[» 3 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  Example with positive numbers

--

--  Argument for rounding functions:

--    positive: at a given number of decimal places

--    zero:     to the nearest integer

--    negative: rounds digits to the left of the decimal point

--

--  ROUND rounds to the nearest value

--  ROUNDDOWN always rounds down

--  ROUNDUP always rounds up

--  TRUNC truncates the decimal part, returning an integer

DEFINE 

    VAR Vals = GENERATESERIES ( 14.90, 16.01, 0.03 )

EVALUATE    

    ADDCOLUMNS (

        Vals,

        "ROUND 0",      ROUND     ( [Value],  0 ),

        "ROUND +1",     ROUND     ( [Value],  1 ),

        "ROUND -1",     ROUND     ( [Value], -1 ),

        "ROUNDDOWN  0", ROUNDDOWN ( [Value],  0 ),

        "ROUNDDOWN +1", ROUNDDOWN ( [Value],  1 ),

        "ROUNDDOWN -1", ROUNDDOWN ( [Value], -1 ),

        "ROUNDUP  0",   ROUNDUP   ( [Value],  0 ),

        "ROUNDUP +1",   ROUNDUP   ( [Value],  1 ),

        "ROUNDUP -1",   ROUNDUP   ( [Value], -1 ),

        "TRUNC",        TRUNC     ( [Value]     )   

    )

ORDER BY [Value] ASC

```

| Value | ROUND 0 | ROUND +1 | ROUND -1 | ROUNDDOWN 0 | ROUNDDOWN +1 | ROUNDDOWN -1 | ROUNDUP 0 | ROUNDUP +1 | ROUNDUP -1 | TRUNC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 14.90 | 15 | 14.90 | 10 | 14 | 14.90 | 10 | 15 | 14.90 | 20 | 14 |
| 14.93 | 15 | 14.90 | 10 | 14 | 14.90 | 10 | 15 | 15.00 | 20 | 14 |
| 14.96 | 15 | 15.00 | 10 | 14 | 14.90 | 10 | 15 | 15.00 | 20 | 14 |
| 14.99 | 15 | 15.00 | 10 | 14 | 14.90 | 10 | 15 | 15.00 | 20 | 14 |
| 15.02 | 15 | 15.00 | 20 | 15 | 15.00 | 10 | 16 | 15.10 | 20 | 15 |
| 15.05 | 15 | 15.10 | 20 | 15 | 15.00 | 10 | 16 | 15.10 | 20 | 15 |
| 15.08 | 15 | 15.10 | 20 | 15 | 15.00 | 10 | 16 | 15.10 | 20 | 15 |
| 15.11 | 15 | 15.10 | 20 | 15 | 15.10 | 10 | 16 | 15.20 | 20 | 15 |
| 15.14 | 15 | 15.10 | 20 | 15 | 15.10 | 10 | 16 | 15.20 | 20 | 15 |
| 15.17 | 15 | 15.20 | 20 | 15 | 15.10 | 10 | 16 | 15.20 | 20 | 15 |
| 15.20 | 15 | 15.20 | 20 | 15 | 15.20 | 10 | 16 | 15.20 | 20 | 15 |
| 15.23 | 15 | 15.20 | 20 | 15 | 15.20 | 10 | 16 | 15.30 | 20 | 15 |
| 15.26 | 15 | 15.30 | 20 | 15 | 15.20 | 10 | 16 | 15.30 | 20 | 15 |
| 15.29 | 15 | 15.30 | 20 | 15 | 15.20 | 10 | 16 | 15.30 | 20 | 15 |
| 15.32 | 15 | 15.30 | 20 | 15 | 15.30 | 10 | 16 | 15.40 | 20 | 15 |
| 15.35 | 15 | 15.40 | 20 | 15 | 15.30 | 10 | 16 | 15.40 | 20 | 15 |
| 15.38 | 15 | 15.40 | 20 | 15 | 15.30 | 10 | 16 | 15.40 | 20 | 15 |
| 15.41 | 15 | 15.40 | 20 | 15 | 15.40 | 10 | 16 | 15.50 | 20 | 15 |
| 15.44 | 15 | 15.40 | 20 | 15 | 15.40 | 10 | 16 | 15.50 | 20 | 15 |
| 15.47 | 15 | 15.50 | 20 | 15 | 15.40 | 10 | 16 | 15.50 | 20 | 15 |
| 15.50 | 16 | 15.50 | 20 | 15 | 15.50 | 10 | 16 | 15.50 | 20 | 15 |
| 15.53 | 16 | 15.50 | 20 | 15 | 15.50 | 10 | 16 | 15.60 | 20 | 15 |
| 15.56 | 16 | 15.60 | 20 | 15 | 15.50 | 10 | 16 | 15.60 | 20 | 15 |
| 15.59 | 16 | 15.60 | 20 | 15 | 15.50 | 10 | 16 | 15.60 | 20 | 15 |
| 15.62 | 16 | 15.60 | 20 | 15 | 15.60 | 10 | 16 | 15.70 | 20 | 15 |
| 15.65 | 16 | 15.70 | 20 | 15 | 15.60 | 10 | 16 | 15.70 | 20 | 15 |
| 15.68 | 16 | 15.70 | 20 | 15 | 15.60 | 10 | 16 | 15.70 | 20 | 15 |
| 15.71 | 16 | 15.70 | 20 | 15 | 15.70 | 10 | 16 | 15.80 | 20 | 15 |
| 15.74 | 16 | 15.70 | 20 | 15 | 15.70 | 10 | 16 | 15.80 | 20 | 15 |
| 15.77 | 16 | 15.80 | 20 | 15 | 15.70 | 10 | 16 | 15.80 | 20 | 15 |
| 15.80 | 16 | 15.80 | 20 | 15 | 15.80 | 10 | 16 | 15.80 | 20 | 15 |
| 15.83 | 16 | 15.80 | 20 | 15 | 15.80 | 10 | 16 | 15.90 | 20 | 15 |
| 15.86 | 16 | 15.90 | 20 | 15 | 15.80 | 10 | 16 | 15.90 | 20 | 15 |
| 15.89 | 16 | 15.90 | 20 | 15 | 15.80 | 10 | 16 | 15.90 | 20 | 15 |
| 15.92 | 16 | 15.90 | 20 | 15 | 15.90 | 10 | 16 | 16.00 | 20 | 15 |
| 15.95 | 16 | 16.00 | 20 | 15 | 15.90 | 10 | 16 | 16.00 | 20 | 15 |
| 15.98 | 16 | 16.00 | 20 | 15 | 15.90 | 10 | 16 | 16.00 | 20 | 15 |
| 16.01 | 16 | 16.00 | 20 | 16 | 16.00 | 10 | 17 | 16.10 | 20 | 16 |

```dax


--  Example with negative numbers

--

--  Argument for rounding functions:

--    positive: at a given number of decimal places

--    zero:     to the nearest integer

--    negative: rounds digits to the left of the decimal point

--

--  ROUND rounds to the nearest value

--  ROUNDDOWN always rounds down

--  ROUNDUP always rounds up

--  TRUNC truncates the decimal part, returning an integer

DEFINE 

    VAR Vals = GENERATESERIES ( -16.01, -14.90, 0.03 )

EVALUATE    

    ADDCOLUMNS (

        Vals,

        "ROUND 0",      ROUND     ( [Value],  0 ),

        "ROUND +1",     ROUND     ( [Value],  1 ),

        "ROUND -1",     ROUND     ( [Value], -1 ),

        "ROUNDDOWN  0", ROUNDDOWN ( [Value],  0 ),

        "ROUNDDOWN +1", ROUNDDOWN ( [Value],  1 ),

        "ROUNDDOWN -1", ROUNDDOWN ( [Value], -1 ),

        "ROUNDUP  0",   ROUNDUP   ( [Value],  0 ),

        "ROUNDUP +1",   ROUNDUP   ( [Value],  1 ),

        "ROUNDUP -1",   ROUNDUP   ( [Value], -1 ),

        "TRUNC",        TRUNC     ( [Value]     )   

    )

ORDER BY [Value] ASC

```

| Value | ROUND 0 | ROUND +1 | ROUND -1 | ROUNDDOWN 0 | ROUNDDOWN +1 | ROUNDDOWN -1 | ROUNDUP 0 | ROUNDUP +1 | ROUNDUP -1 | TRUNC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| -16.01 | -16 | -16.00 | -20 | -16 | -16.00 | -10 | -17 | -16.10 | -20 | -16 |
| -15.98 | -16 | -16.00 | -20 | -15 | -15.90 | -10 | -16 | -16.00 | -20 | -15 |
| -15.95 | -16 | -16.00 | -20 | -15 | -15.90 | -10 | -16 | -16.00 | -20 | -15 |
| -15.92 | -16 | -15.90 | -20 | -15 | -15.90 | -10 | -16 | -16.00 | -20 | -15 |
| -15.89 | -16 | -15.90 | -20 | -15 | -15.80 | -10 | -16 | -15.90 | -20 | -15 |
| -15.86 | -16 | -15.90 | -20 | -15 | -15.80 | -10 | -16 | -15.90 | -20 | -15 |
| -15.83 | -16 | -15.80 | -20 | -15 | -15.80 | -10 | -16 | -15.90 | -20 | -15 |
| -15.80 | -16 | -15.80 | -20 | -15 | -15.80 | -10 | -16 | -15.80 | -20 | -15 |
| -15.77 | -16 | -15.80 | -20 | -15 | -15.70 | -10 | -16 | -15.80 | -20 | -15 |
| -15.74 | -16 | -15.70 | -20 | -15 | -15.70 | -10 | -16 | -15.80 | -20 | -15 |
| -15.71 | -16 | -15.70 | -20 | -15 | -15.70 | -10 | -16 | -15.80 | -20 | -15 |
| -15.68 | -16 | -15.70 | -20 | -15 | -15.60 | -10 | -16 | -15.70 | -20 | -15 |
| -15.65 | -16 | -15.70 | -20 | -15 | -15.60 | -10 | -16 | -15.70 | -20 | -15 |
| -15.62 | -16 | -15.60 | -20 | -15 | -15.60 | -10 | -16 | -15.70 | -20 | -15 |
| -15.59 | -16 | -15.60 | -20 | -15 | -15.50 | -10 | -16 | -15.60 | -20 | -15 |
| -15.56 | -16 | -15.60 | -20 | -15 | -15.50 | -10 | -16 | -15.60 | -20 | -15 |
| -15.53 | -16 | -15.50 | -20 | -15 | -15.50 | -10 | -16 | -15.60 | -20 | -15 |
| -15.50 | -16 | -15.50 | -20 | -15 | -15.50 | -10 | -16 | -15.50 | -20 | -15 |
| -15.47 | -15 | -15.50 | -20 | -15 | -15.40 | -10 | -16 | -15.50 | -20 | -15 |
| -15.44 | -15 | -15.40 | -20 | -15 | -15.40 | -10 | -16 | -15.50 | -20 | -15 |
| -15.41 | -15 | -15.40 | -20 | -15 | -15.40 | -10 | -16 | -15.50 | -20 | -15 |
| -15.38 | -15 | -15.40 | -20 | -15 | -15.30 | -10 | -16 | -15.40 | -20 | -15 |
| -15.35 | -15 | -15.40 | -20 | -15 | -15.30 | -10 | -16 | -15.40 | -20 | -15 |
| -15.32 | -15 | -15.30 | -20 | -15 | -15.30 | -10 | -16 | -15.40 | -20 | -15 |
| -15.29 | -15 | -15.30 | -20 | -15 | -15.20 | -10 | -16 | -15.30 | -20 | -15 |
| -15.26 | -15 | -15.30 | -20 | -15 | -15.20 | -10 | -16 | -15.30 | -20 | -15 |
| -15.23 | -15 | -15.20 | -20 | -15 | -15.20 | -10 | -16 | -15.30 | -20 | -15 |
| -15.20 | -15 | -15.20 | -20 | -15 | -15.20 | -10 | -16 | -15.20 | -20 | -15 |
| -15.17 | -15 | -15.20 | -20 | -15 | -15.10 | -10 | -16 | -15.20 | -20 | -15 |
| -15.14 | -15 | -15.10 | -20 | -15 | -15.10 | -10 | -16 | -15.20 | -20 | -15 |
| -15.11 | -15 | -15.10 | -20 | -15 | -15.10 | -10 | -16 | -15.20 | -20 | -15 |
| -15.08 | -15 | -15.10 | -20 | -15 | -15.00 | -10 | -16 | -15.10 | -20 | -15 |
| -15.05 | -15 | -15.10 | -20 | -15 | -15.00 | -10 | -16 | -15.10 | -20 | -15 |
| -15.02 | -15 | -15.00 | -20 | -15 | -15.00 | -10 | -16 | -15.10 | -20 | -15 |
| -14.99 | -15 | -15.00 | -10 | -14 | -14.90 | -10 | -15 | -15.00 | -20 | -14 |
| -14.96 | -15 | -15.00 | -10 | -14 | -14.90 | -10 | -15 | -15.00 | -20 | -14 |
| -14.93 | -15 | -14.90 | -10 | -14 | -14.90 | -10 | -15 | -15.00 | -20 | -14 |
| -14.90 | -15 | -14.90 | -10 | -14 | -14.90 | -10 | -15 | -14.90 | -20 | -14 |

## Related articles

Learn more about TRUNC in the following articles:

- [**Creating a simple date table in DAX**](https://www.sqlbi.com/articles/creating-a-simple-date-table-in-dax/)

  This article shows how to build a basic date table using a calculated table and DAX. [» Read more](https://www.sqlbi.com/articles/creating-a-simple-date-table-in-dax/)
- [**Introducing dynamic format strings for DAX measures**](https://www.sqlbi.com/articles/introducing-dynamic-format-strings-for-dax-measures/)

  Power BI introduced dynamic format strings in May 2023. In this article, we briefly introduce this functionality and then dive into a few technical details of its implementation. [» Read more](https://www.sqlbi.com/articles/introducing-dynamic-format-strings-for-dax-measures/)
- [**Improving data labels with format strings**](https://www.sqlbi.com/articles/improving-data-labels-with-format-strings/)

  This article describes the different approaches to format your DAX measures in Power BI semantic models using format custom and dynamic format strings. [» Read more](https://www.sqlbi.com/articles/improving-data-labels-with-format-strings/)

## Related functions

Other related functions are:

- [[ROUND]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/trunc-function-dax](https://docs.microsoft.com/en-us/dax/trunc-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
