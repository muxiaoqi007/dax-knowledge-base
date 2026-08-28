---
title: "DEGREES"
function: "degrees"
category: "Math and Trig"
url: "https://dax.guide/degrees/"
source: "dax.guide"
重要度:
难度:
---

# DEGREES DAX Function (Math and Trig)

Converts radians into degrees.

## Syntax

DEGREES ( <Number> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | The angle in radians that you want to convert. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

[» 1 related function](#alt)  

## Examples

```dax


--  RADIANS and DEGREES convert one unit of measure

--  in the other one, for angles.

DEFINE 

    VAR Vals = GENERATESERIES ( -PI(), PI(), PI()/4 )

EVALUATE

    ADDCOLUMNS ( 

        Vals,

        "DEGREES",   DEGREES( [Value] ),

        "RADIANS",   RADIANS ( DEGREES ( [Value] ) )

    )

```

| Value | DEGREES | RADIANS |
| --- | --- | --- |
| -3.14 | -180 | -3.14 |
| -2.36 | -135 | -2.36 |
| -1.57 | -90 | -1.57 |
| -0.79 | -45 | -0.79 |
| 0.00 | 0 | 0.00 |
| 0.79 | 45 | 0.79 |
| 1.57 | 90 | 1.57 |
| 2.36 | 135 | 2.36 |
| 3.14 | 180 | 3.14 |

## Related functions

Other related functions are:

- [[RADIANS]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/degrees-function-dax](https://docs.microsoft.com/en-us/dax/degrees-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
