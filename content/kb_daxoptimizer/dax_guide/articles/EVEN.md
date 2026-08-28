---
title: "EVEN"
function: "even"
category: "Math and Trig"
url: "https://dax.guide/even/"
source: "dax.guide"
重要度:
难度:
---

# EVEN DAX Function (Math and Trig)

Returns number rounded up to the nearest even integer. You can use this function for processing items that come in twos. For example, a packing crate accepts rows of one or two items. The crate is full when the number of items, rounded up to the nearest two, matches the crate’s capacity.

## Syntax

EVEN ( <Number> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | The value to round. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

Returns number rounded up to the nearest even integer.

## Remarks

If number is nonnumeric, EVEN returns the #[[VALUE]]! error value.

Regardless of the sign of number, a value is rounded up when adjusted away from zero. If number is an even integer, no rounding occurs.

[» 1 related function](#alt)  

## Examples

```dax


--  ODD and EVEN round to an even or odd number.

--  The rounding happens by finding the nearest larger odd/even integer,

--  considering the absolute value of the number.

DEFINE

    VAR Vals =

        GENERATESERIES ( -5, +5, 2.1 )

EVALUATE

ADDCOLUMNS ( 

    Vals, 

    "EVEN", EVEN ( [Value] ), 

    "ODD", ODD ( [Value] )

)

ORDER BY [Value] ASC

```

| Value | EVEN | ODD |
| --- | --- | --- |
| -5.00 | -6 | -5 |
| -2.90 | -4 | -3 |
| -0.80 | -2 | -1 |
| 1.30 | 2 | 3 |
| 3.40 | 4 | 5 |

## Related functions

Other related functions are:

- [[ODD]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/even-function-dax](https://docs.microsoft.com/en-us/dax/even-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
