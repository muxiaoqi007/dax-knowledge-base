---
title: "LCM"
function: "lcm"
category: "Math and Trig"
url: "https://dax.guide/lcm/"
source: "dax.guide"
重要度:
难度:
---

# LCM DAX Function (Math and Trig)

Returns the least common multiple of integers. The least common multiple is the smallest positive integer that is a multiple of both integer arguments number1, number2. Use LCM to add fractions with different denominators.

## Syntax

LCM ( <Number1>, <Number2> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number1 |  | The first number, if value is not an integer, it is truncated. |
| Number2 |  | The second number, if value is not an integer, it is truncated. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

Returns the least common multiple of integers.

## Examples

```dax


--  GCD - Returns the greatest common divisor of two or more integers

--  LCM - Returns the least common multiple of integers

DEFINE

    VAR Val1 = SELECTCOLUMNS ( GENERATESERIES ( 6, 25, 7 ), "Val1", [Value] )

    VAR Val2 = SELECTCOLUMNS ( GENERATESERIES ( 2, 3, 1 ), "Val2", [Value] )

EVALUATE

ADDCOLUMNS ( 

    CROSSJOIN ( Val1, Val2 ),

    "GCD", GCD ( [Val1], [Val2] ),

    "LCM", LCM ( [Val1], [Val2] )

)

ORDER BY [Val1], [Val2]

```

| Val1 | Val2 | GCD | LCM |
| --- | --- | --- | --- |
| 6 | 2 | 2 | 6 |
| 6 | 3 | 3 | 6 |
| 13 | 2 | 1 | 26 |
| 13 | 3 | 1 | 39 |
| 20 | 2 | 2 | 20 |
| 20 | 3 | 1 | 60 |

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/lcm-function-dax](https://docs.microsoft.com/en-us/dax/lcm-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
