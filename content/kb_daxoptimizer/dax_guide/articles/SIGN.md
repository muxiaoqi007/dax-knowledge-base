---
title: "SIGN"
function: "sign"
category: "Math and Trig"
url: "https://dax.guide/sign/"
source: "dax.guide"
重要度:
难度:
---

# SIGN DAX Function (Math and Trig)

Returns the sign of a number: 1 if the number is positive, zero if the number is zero, or -1 if the number is negative.

## Syntax

SIGN ( <Number> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | Any real number. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

The possible results are:

- 1 if the number is positive
- 0 if the number is zero
- -1 if the number is negative

## Remarks

It does not work with infinite numbers.

## Examples

```dax


--  ABS returns the absolute value of a number

--  SIGN returns:

--      +1 if the number is positive

--       0 if the number is zero

--      -1 if the number is negative

DEFINE

    VAR Vals = GENERATESERIES ( -2, +2, 0.5 )

EVALUATE

ADDCOLUMNS ( 

    Vals, 

    "ABS", ABS ( [Value] ), 

    "SIGN", SIGN ( [Value] )

)

ORDER BY [Value] DESC

```

| Value | ABS | SIGN |
| --- | --- | --- |
| 2.00 | 2.00 | 1 |
| 1.50 | 1.50 | 1 |
| 1.00 | 1.00 | 1 |
| 0.50 | 0.50 | 1 |
| 0.00 | 0.00 | 0 |
| -0.50 | 0.50 | -1 |
| -1.00 | 1.00 | -1 |
| -1.50 | 1.50 | -1 |
| -2.00 | 2.00 | -1 |

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/sign-function-dax](https://docs.microsoft.com/en-us/dax/sign-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
