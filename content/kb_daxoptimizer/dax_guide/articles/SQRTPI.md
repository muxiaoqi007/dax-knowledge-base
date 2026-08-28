---
title: "SQRTPI"
function: "sqrtpi"
category: "Math and Trig"
url: "https://dax.guide/sqrtpi/"
source: "dax.guide"
重要度:
难度:
---

# SQRTPI DAX Function (Math and Trig)

Returns the square root of (number \* pi).

## Syntax

SQRTPI ( <Number> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | The number by which pi is multiplied. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Returns the square root of (number \* π).

## Remarks

The Microsoft documentation and IntelliSense use “pi” as an alias for π.

[» 1 related function](#alt)  

## Examples

```dax


--  SQRTPI computes the square root of the value multiplied by Pi

--  PI returns the value of Pi with 15 digits of accuracy

DEFINE

    VAR Vals = GENERATESERIES ( 1, 25, 3 )

EVALUATE

ADDCOLUMNS ( 

    Vals,

    "SQRTPI",       SQRTPI ( [Value] ),

    "SQRT and PI",  SQRT   ( [Value] * PI () )

)

```

| Value | SQRTPI | SQRT and PI |
| --- | --- | --- |
| 1 | 1.77 | 1.77 |
| 4 | 3.54 | 3.54 |
| 7 | 4.69 | 4.69 |
| 10 | 5.60 | 5.60 |
| 13 | 6.39 | 6.39 |
| 16 | 7.09 | 7.09 |
| 19 | 7.73 | 7.73 |
| 22 | 8.31 | 8.31 |
| 25 | 8.86 | 8.86 |

## Related functions

Other related functions are:

- [[SQRT]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/sqrtpi-function-dax](https://docs.microsoft.com/en-us/dax/sqrtpi-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
