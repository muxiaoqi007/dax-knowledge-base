---
title: "LN"
function: "ln"
category: "Math and Trig"
url: "https://dax.guide/ln/"
source: "dax.guide"
重要度:
难度:
---

# LN DAX Function (Math and Trig)

Returns the natural logarithm of a number.

## Syntax

LN ( <Number> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | The positive number for which you want the natural logarithm. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

The natural logarithm of the number passed as an argument.

## Remarks

Returns the natural logarithm of a number.  
Natural logarithms are based on the constant e (2.71828182845904).  
LN is the inverse of the [[EXP]] function.  
If the number is less than or equal to zero, the function generates an error.

[» 3 related functions](#alt)  

## Examples

```dax


--  Logarithmic functions include LOG10, LN, LOG, and EXP to compute

--  logarithms using various bases or exponential.

DEFINE

    VAR Vals = GENERATESERIES ( 1, 25, 4 )

EVALUATE

ADDCOLUMNS ( 

    Vals,

    "LN",       LN      ( [Value] ),

    "LOG10",    LOG10   ( [Value] ),

    "LOG 5",    LOG     ( [Value], 5 ),

    "EXP",      EXP     ( [Value] )

)



```

| Value | LN | LOG10 | LOG 5 | EXP |
| --- | --- | --- | --- | --- |
| 1 | 0.00 | 0.00 | 0.00 | 2.72 |
| 5 | 1.61 | 0.70 | 1.00 | 148.41 |
| 9 | 2.20 | 0.95 | 1.37 | 8,103.08 |
| 13 | 2.56 | 1.11 | 1.59 | 442,413.39 |
| 17 | 2.83 | 1.23 | 1.76 | 24,154,952.75 |
| 21 | 3.04 | 1.32 | 1.89 | 1,318,815,734.48 |
| 25 | 3.22 | 1.40 | 2.00 | 72,004,899,337.39 |

## Related functions

Other related functions are:

- [[EXP]]
- [[LOG]]
- [[LOG10]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/ln-function-dax](https://docs.microsoft.com/en-us/dax/ln-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
