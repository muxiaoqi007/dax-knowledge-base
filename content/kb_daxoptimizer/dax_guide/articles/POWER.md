---
title: "POWER"
function: "power"
category: "Math and Trig"
url: "https://dax.guide/power/"
source: "dax.guide"
重要度:
难度:
---

# POWER DAX Function (Math and Trig)

Returns the result of a number raised to a power.

## Syntax

POWER ( <Number>, <Power> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | The base number. |
| Power |  | The exponent, to which the base number is raised. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

The Number raised to Power.

## Remarks

The [exponentiation operator ^](https://dax.guide/op/exponentiation/) returns the same result as the POWER function.  
The following two expressions are equivalent.

```dax


5 ^ 4

POWER ( 5, 4 )

```

[» 1 related article](#articles)  

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

## Related articles

Learn more about POWER in the following articles:

- [**Using EXPAND and COLLAPSE in visual calculations**](https://www.sqlbi.com/articles/using-expand-and-collapse-in-visual-calculations/)

  This article provides examples of visual calculations where the use of EXPAND and COLLAPSE is required to obtain the correct result. [» Read more](https://www.sqlbi.com/articles/using-expand-and-collapse-in-visual-calculations/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/power-function-dax](https://docs.microsoft.com/en-us/dax/power-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
