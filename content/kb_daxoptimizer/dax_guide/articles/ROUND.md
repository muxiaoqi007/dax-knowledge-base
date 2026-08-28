---
title: "ROUND"
function: "round"
category: "Math and Trig"
url: "https://dax.guide/round/"
source: "dax.guide"
重要度:
难度:
---

# ROUND DAX Function (Math and Trig)

Rounds a number to a specified number of digits.

## Syntax

ROUND ( <Number>, <NumberOfDigits> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | The number you want to round. |
| NumberOfDigits |  | The number of digits to which you want to round. Negative rounds to the left of the decimal point; zero to the nearest integer. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

The rounded number.

## Remarks

If NumberOfDigits is greater than 0 (zero), then number is rounded to the specified number of decimal places.

If NumberOfDigits is 0, the number is rounded to the nearest integer.

If NumberOfDigits is less than 0, the number is rounded to the left of the decimal point.

[» 2 related articles](#articles)  
[» 7 related functions](#alt)  

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

Learn more about ROUND in the following articles:

- [**Optimizing callbacks in a SUMX iterator**](https://www.sqlbi.com/articles/optimizing-callbacks-in-a-sumx-iterator/)

  This article explains a typical pattern to optimize a SUMX iterator by reducing the number of callbacks in the expression. [» Read more](https://www.sqlbi.com/articles/optimizing-callbacks-in-a-sumx-iterator/)
- [**Optional parameters in DAX user-defined functions**](https://www.sqlbi.com/articles/optional-parameters-in-dax-user-defined-functions/)

  This article describes how to define optional parameters in DAX user-defined functions and set default values for parameters not specified by the caller. [» Read more](https://www.sqlbi.com/articles/optional-parameters-in-dax-user-defined-functions/)

## Related functions

Other related functions are:

- [[MROUND]]
- [[ROUNDDOWN]]
- [[ROUNDUP]]
- [[CEILING]]
- [[FIXED]]
- [[FLOOR]]
- [[ISO.CEILING]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/round-function-dax](https://docs.microsoft.com/en-us/dax/round-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
