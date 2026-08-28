---
title: "ATANH"
function: "atanh"
category: "Math and Trig"
url: "https://dax.guide/atanh/"
source: "dax.guide"
重要度:
难度:
---

# ATANH DAX Function (Math and Trig)

Returns the inverse hyperbolic tangent of a number. Number must be between -1 and 1 (excluding -1 and 1). The inverse hyperbolic tangent is the value whose hyperbolic tangent is number, so ATANH([[TANH]](number)) equals number.

## Syntax

ATANH ( <Number> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | Any real number between 1 and -1. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

## Remarks

Returns the inverse hyperbolic tangent of a number.

## Examples

```dax


--  SINH, COSH, ASINH, ACOSH, TANH, ATANH, COTH, ACOTH

--  are the standard hyperbolic trigonometrical functions.

--

--  Where required, the arguments are specified in radians.

DEFINE

    VAR Vals = GENERATESERIES ( -PI()+0.001, PI(), PI()/4 )

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

        "SINH",      SINH ( [Value] ),

        "ASINH",     ASINH ( SINH ( [Value] ) ),

        "COSH",      COSH ( [Value] ),

        "ACOSH",     ACOSH ( COSH ( [Value] ) ),

        "TANH",      TANH ( [Value] ),

        "ATANH",     ATANH ( TANH ( [Value] ) ),

        "COTH",      COTH ( [Value] ),

        "ACOTH",     ACOTH ( COTH ( [Value] ) )

    )

```

| Value | Value (nπ) | SINH | ASINH | COSH | ACOSH | TANH | ATANH | COTH | ACOTH |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| -3.14 | -π | -11.54 | -3.14 | 11.58 | 3.14 | -1.00 | -3.14 | -1.00 | -3.14 |
| -2.36 | -¾π | -5.22 | -2.36 | 5.32 | 2.36 | -0.98 | -2.36 | -1.02 | -2.36 |
| -1.57 | -½π | -2.30 | -1.57 | 2.51 | 1.57 | -0.92 | -1.57 | -1.09 | -1.57 |
| -0.78 | -¼π | -0.87 | -0.78 | 1.32 | 0.78 | -0.66 | -0.78 | -1.53 | -0.78 |
| 0.00 | 0 | 0.00 | 0.00 | 1.00 | 0.00 | 0.00 | 0.00 | 1,000.00 | 0.00 |
| 0.79 | ¼π | 0.87 | 0.79 | 1.33 | 0.79 | 0.66 | 0.79 | 1.52 | 0.79 |
| 1.57 | ½π | 2.30 | 1.57 | 2.51 | 1.57 | 0.92 | 1.57 | 1.09 | 1.57 |
| 2.36 | ¾π | 5.23 | 2.36 | 5.33 | 2.36 | 0.98 | 2.36 | 1.02 | 2.36 |

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/atanh-function-dax](https://docs.microsoft.com/en-us/dax/atanh-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
