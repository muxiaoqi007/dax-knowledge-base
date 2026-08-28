---
title: "COT"
function: "cot"
category: "Math and Trig"
url: "https://dax.guide/cot/"
source: "dax.guide"
重要度:
难度:
---

# COT DAX Function (Math and Trig)

Return the cotangent of an angle specified in radians.

## Syntax

COT ( <Number> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | The angle in radians for which you want the cotangent. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Return the cotangent of an angle specified in radians.

[» 1 related function](#alt)  

## Examples

```dax


--  SIN, COS, ASIN, ACOS, TAN, ATAN, COT, ACOT are the

--  standard trigonometrical functions.

--

--  Where required, the arguments are specified in radians.

DEFINE

    VAR Vals = GENERATESERIES ( -3 * PI () + 0.001, 3 * PI (), PI () / 4 )



EVALUATE

ADDCOLUMNS (

    Vals,

    "Value (nπ)",

        VAR ImproperQuartersOfPi = ROUND ( ABS ( [Value] / PI () * 4 ), 0 )

        VAR WholePis = ROUNDDOWN ( ImproperQuartersOfPi / 4, 0 )

        VAR ProperQuartersOfPi = MOD ( ImproperQuartersOfPi, 4 )

        RETURN

            IF ( ROUND ( [Value], 0 ) < 0, "-" )

                & IF ( WholePis <> 0 && ImproperQuartersOfPi <> 4, WholePis )

                & IF ( ProperQuartersOfPi <> 0, UNICHAR ( 187 + ProperQuartersOfPi ) )

                & IF ( ImproperQuartersOfPi = 0, "0", "π" ),

    "SIN", SIN ( [Value] ),

    "ASIN", ASIN ( SIN ( [Value] ) ),

    "COS", COS ( [Value] ),

    "ACOS", ACOS ( COS ( [Value] ) ),

    "TAN", TAN ( [Value] ),

    "ATAN", ATAN ( TAN ( [Value] ) ),

    "COT", COT ( [Value] ),

    "ACOT", ACOT ( COT ( [Value] ) )

)

```

| Value | Value (nπ) | SIN | ASIN | COS | ACOS | TAN | ATAN | COT | ACOT |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| -9.42 | -3π | -0.00 | -0.00 | -1.00 | 3.14 | 0.00 | 0.00 | 1,000.00 | 0.00 |
| -8.64 | -2¾π | -0.71 | -0.79 | -0.71 | 2.36 | 1.00 | 0.79 | 1.00 | 0.79 |
| -7.85 | -2½π | -1.00 | -1.57 | 0.00 | 1.57 | -1,000.00 | -1.57 | -0.00 | 1.57 |
| -7.07 | -2¼π | -0.71 | -0.78 | 0.71 | 0.78 | -1.00 | -0.78 | -1.00 | 2.36 |
| -6.28 | -2π | 0.00 | 0.00 | 1.00 | 0.00 | 0.00 | 0.00 | 1,000.00 | 0.00 |
| -5.50 | -1¾π | 0.71 | 0.79 | 0.71 | 0.79 | 1.00 | 0.79 | 1.00 | 0.79 |
| -4.71 | -1½π | 1.00 | 1.57 | -0.00 | 1.57 | -1,000.00 | -1.57 | -0.00 | 1.57 |
| -3.93 | -1¼π | 0.71 | 0.78 | -0.71 | 2.36 | -1.00 | -0.78 | -1.00 | 2.36 |
| -3.14 | -π | -0.00 | -0.00 | -1.00 | 3.14 | 0.00 | 0.00 | 1,000.00 | 0.00 |
| -2.36 | -¾π | -0.71 | -0.79 | -0.71 | 2.36 | 1.00 | 0.79 | 1.00 | 0.79 |
| -1.57 | -½π | -1.00 | -1.57 | 0.00 | 1.57 | -1,000.00 | -1.57 | -0.00 | 1.57 |
| -0.78 | -¼π | -0.71 | -0.78 | 0.71 | 0.78 | -1.00 | -0.78 | -1.00 | 2.36 |
| 0.00 | 0 | 0.00 | 0.00 | 1.00 | 0.00 | 0.00 | 0.00 | 1,000.00 | 0.00 |
| 0.79 | ¼π | 0.71 | 0.79 | 0.71 | 0.79 | 1.00 | 0.79 | 1.00 | 0.79 |
| 1.57 | ½π | 1.00 | 1.57 | -0.00 | 1.57 | -1,000.00 | -1.57 | -0.00 | 1.57 |
| 2.36 | ¾π | 0.71 | 0.78 | -0.71 | 2.36 | -1.00 | -0.78 | -1.00 | 2.36 |
| 3.14 | π | -0.00 | -0.00 | -1.00 | 3.14 | 0.00 | 0.00 | 1,000.00 | 0.00 |
| 3.93 | 1¼π | -0.71 | -0.79 | -0.71 | 2.36 | 1.00 | 0.79 | 1.00 | 0.79 |
| 4.71 | 1½π | -1.00 | -1.57 | 0.00 | 1.57 | -1,000.00 | -1.57 | -0.00 | 1.57 |
| 5.50 | 1¾π | -0.71 | -0.78 | 0.71 | 0.78 | -1.00 | -0.78 | -1.00 | 2.36 |
| 6.28 | 2π | 0.00 | 0.00 | 1.00 | 0.00 | 0.00 | 0.00 | 1,000.00 | 0.00 |
| 7.07 | 2¼π | 0.71 | 0.79 | 0.71 | 0.79 | 1.00 | 0.79 | 1.00 | 0.79 |
| 7.85 | 2½π | 1.00 | 1.57 | -0.00 | 1.57 | -1,000.00 | -1.57 | -0.00 | 1.57 |
| 8.64 | 2¾π | 0.71 | 0.78 | -0.71 | 2.36 | -1.00 | -0.78 | -1.00 | 2.36 |

## Related functions

Other related functions are:

- [[COTH]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/cot-function-dax](https://docs.microsoft.com/en-us/dax/cot-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
