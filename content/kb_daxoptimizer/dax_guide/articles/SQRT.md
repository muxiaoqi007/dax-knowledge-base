---
title: "SQRT"
function: "sqrt"
category: "Math and Trig"
url: "https://dax.guide/sqrt/"
source: "dax.guide"
重要度:
难度:
---

# SQRT DAX Function (Math and Trig)

Returns the square root of a number.

## Syntax

SQRT ( <Number> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | The number for which you want the square root. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Square root of Number.

## Remarks

If the number is negative, the SQRT function returns an error.

[» 1 related function](#alt)  

## Examples

```dax


--  SQRT computes the square root of a number

--  POWER raises the number to the required power

DEFINE

    VAR Vals = GENERATESERIES ( 1, 25, 3 )

EVALUATE

ADDCOLUMNS ( 

    Vals,

    "SQRT",        SQRT   ( [Value] ),

    "POWER (0.5)", POWER  ( [Value], 0.5 ),

    "POWER (4)",   POWER  ( [Value],   4 ),

    "POWER  -1",   POWER  ( [Value],  -1 )

)



```

| Value | SQRT | POWER (0.5) | POWER (4) | POWER -1 |
| --- | --- | --- | --- | --- |
| 1 | 1.00 | 1.00 | 1 | 1.00 |
| 4 | 2.00 | 2.00 | 256 | 0.25 |
| 7 | 2.65 | 2.65 | 2,401 | 0.14 |
| 10 | 3.16 | 3.16 | 10,000 | 0.10 |
| 13 | 3.61 | 3.61 | 28,561 | 0.08 |
| 16 | 4.00 | 4.00 | 65,536 | 0.06 |
| 19 | 4.36 | 4.36 | 130,321 | 0.05 |
| 22 | 4.69 | 4.69 | 234,256 | 0.05 |
| 25 | 5.00 | 5.00 | 390,625 | 0.04 |

## Related functions

Other related functions are:

- [[SQRTPI]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/sqrt-function-dax](https://docs.microsoft.com/en-us/dax/sqrt-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
