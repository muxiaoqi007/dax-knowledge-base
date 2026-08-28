---
title: "COMBIN"
function: "combin"
category: "Statistical"
url: "https://dax.guide/combin/"
source: "dax.guide"
重要度:
难度:
---

# COMBIN DAX Function (Statistical)

Returns the number of combinations for a given number of items. Use COMBIN to determine the total possible number of groups for a given number of items.

## Syntax

COMBIN ( <Number>, <Number\_chosen> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | The number of items. |
| Number\_chosen |  | The number of items in each combination. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

Returns the number of combinations for a given number of items.

## Remarks

Numeric arguments are truncated to integers.

If either argument is nonnumeric, COMBIN returns the #[[VALUE]]! error value.

If number < 0, number\_chosen < 0, or number < number\_chosen, COMBIN returns the #NUM! error value.

A combination is any set or subset of items, regardless of their internal order. Combinations are distinct from permutations, for which the internal order is significant.

The equation for the number of combinations is: P( n, k ) = n! / ( k! ( n – k)! )

This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.

[» 2 related functions](#alt)  

## Examples

```dax


--  COMBIN returns the number of possible combinations of

--  a given number of items WITHOUT repetitions.

--  The internal order is not relevant.

--  Use PERMUT for the number of permutations, 

--  where the internal order is relevant.

--

--  Use COMBINA to compute combinations WITH repetitions.

--

--  COMBIN implements this calculation:

--  P(n,k) = n! / k!(n-k)!

--

--  For example, consider 3 elements ( A, B, C )

--  COMBIN ( 3, 2 ) returns 3 counting:

--       ( A, B )

--       ( B, C )

--       ( A, C )

EVALUATE 

{

    ( "COMBIN ( 3, 1 )", COMBIN ( 3, 1 ) ),

    ( "COMBIN ( 3, 2 )", COMBIN ( 3, 2 ) ),

    ( "COMBIN ( 3, 3 )", COMBIN ( 3, 3 ) ),

    ( "COMBIN ( 4, 1 )", COMBIN ( 4, 1 ) ),

    ( "COMBIN ( 4, 2 )", COMBIN ( 4, 2 ) ),

    ( "COMBIN ( 4, 3 )", COMBIN ( 4, 3 ) ),

    ( "COMBIN ( 4, 4 )", COMBIN ( 4, 4 ) )

}



```

| Value1 | Value2 |
| --- | --- |
| COMBIN ( 3, 1 ) | 3 |
| COMBIN ( 3, 2 ) | 3 |
| COMBIN ( 3, 3 ) | 1 |
| COMBIN ( 4, 1 ) | 4 |
| COMBIN ( 4, 2 ) | 6 |
| COMBIN ( 4, 3 ) | 4 |
| COMBIN ( 4, 4 ) | 1 |

## Related functions

Other related functions are:

- [[COMBINA]]
- [[PERMUT]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/combin-function-dax](https://docs.microsoft.com/en-us/dax/combin-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
