---
title: "COMBINA"
function: "combina"
category: "Statistical"
url: "https://dax.guide/combina/"
source: "dax.guide"
重要度:
难度:
---

# COMBINA DAX Function (Statistical)

Returns the number of combinations (with repetitions) for a given number of items.

## Syntax

COMBINA ( <Number>, <Number\_chosen> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | Must be greater than or equal to 0, and greater than or equal to Number\_chosen. Non-integer values are truncated. |
| Number\_chosen |  | Must be greater than or equal to 0. Non-integer values are truncated. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

Returns the number of combinations (with repetitions) for a given number of items.

## Remarks

Numeric arguments are rounded to integers.

If the value of either argument is outside of its constraints, COMBINA returns the #NUM! error value.

If either argument is a non-numeric value, COMBINA returns the #[[VALUE]]! error value.

The equation for the number of combinations (with repetitions) is C( N + M − 1, N − 1).

This function is not supported in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.

[» 2 related functions](#alt)  

## Examples

```dax


--  COMBINA returns the number of possible combinations of

--  a given number of items WITH repetitions.

--  The internal order is not relevant.

--  Use COMBIN to compute combinations WITHOUT repetitions.

--

--  For example, consider 3 elements ( A, B, C )

--  COMBINA ( 3, 2 ) returns 6 counting:

--       ( A, A )

--       ( B, B )

--       ( C, C )

--       ( A, B )

--       ( B, C )

--       ( A, C )

EVALUATE 

{

    ( "COMBINA ( 3, 1 )", COMBINA ( 3, 1 ) ),

    ( "COMBINA ( 3, 2 )", COMBINA ( 3, 2 ) ),

    ( "COMBINA ( 3, 3 )", COMBINA ( 3, 3 ) ),

    ( "COMBINA ( 4, 1 )", COMBINA ( 4, 1 ) ),

    ( "COMBINA ( 4, 2 )", COMBINA ( 4, 2 ) ),

    ( "COMBINA ( 4, 3 )", COMBINA ( 4, 3 ) ),

    ( "COMBINA ( 4, 4 )", COMBINA ( 4, 4 ) )

}



```

| Value1 | Value2 |
| --- | --- |
| COMBINA ( 3, 1 ) | 3 |
| COMBINA ( 3, 2 ) | 6 |
| COMBINA ( 3, 3 ) | 10 |
| COMBINA ( 4, 1 ) | 4 |
| COMBINA ( 4, 2 ) | 10 |
| COMBINA ( 4, 3 ) | 20 |
| COMBINA ( 4, 4 ) | 35 |

## Related functions

Other related functions are:

- [[COMBIN]]
- [[PERMUT]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/combina-function-dax](https://docs.microsoft.com/en-us/dax/combina-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
