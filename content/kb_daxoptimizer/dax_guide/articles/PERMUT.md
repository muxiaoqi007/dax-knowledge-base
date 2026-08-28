---
title: "PERMUT"
function: "permut"
category: "Statistical"
url: "https://dax.guide/permut/"
source: "dax.guide"
重要度:
难度:
---

# PERMUT DAX Function (Statistical)

Returns the number of permutations for a given number of objects that can be selected from number objects. A permutation is any set or subset of objects or events where internal order is significant. Permutations are different from combinations, for which the internal order is not significant. Use this function for lottery-style probability calculations.

## Syntax

PERMUT ( <Number>, <Number\_chosen> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | An integer that describes the number of objects. |
| Number\_chosen |  | An integer that describes the number of objects in each permutation. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Returns the number of permutations for a given number of objects that can be selected from number objects.

## Remarks

Both arguments are truncated to integers.

PERMUT raises an error when:

- Number <= 0
- number\_chosen < 0
- Number < number\_chosen

The equation for the number of permutations is: P( n, k ) = n! / ( n – k)!

[» 2 related functions](#alt)  

## Examples

```dax


--  PERMUT returns the number of permutations for a given

--  number of items selected from a number of objects.

--  A permutation is any set or subset of objects where the

--  internal order is relevant.

--

--  Use COMBIN for the number of combinations, 

--  where the internal order is not relevant.

--

--  PERMUT implements this calculation:

--  P(n,k) = n! / (n-k)!

--

--  For example, consider 3 elements ( A, B, C )

--  PERMUT ( 3, 2 ) returns 6 counting:

--       ( A, B )

--       ( B, A )

--       ( B, C )

--       ( C, B )

--       ( A, C )

--       ( C, A )

EVALUATE 

{

    ( "PERMUT ( 2, 1 )", PERMUT ( 2, 1 ) ),

    ( "PERMUT ( 2, 2 )", PERMUT ( 2, 2 ) ),

    ( "PERMUT ( 3, 1 )", PERMUT ( 3, 1 ) ),

    ( "PERMUT ( 3, 2 )", PERMUT ( 3, 2 ) ),

    ( "PERMUT ( 3, 3 )", PERMUT ( 3, 3 ) ),

    ( "PERMUT ( 4, 1 )", PERMUT ( 4, 1 ) ),

    ( "PERMUT ( 4, 2 )", PERMUT ( 4, 2 ) ),

    ( "PERMUT ( 4, 3 )", PERMUT ( 4, 3 ) ),

    ( "PERMUT ( 4, 4 )", PERMUT ( 4, 4 ) )

}



```

| Value1 | Value2 |
| --- | --- |
| PERMUT ( 2, 1 ) | 2 |
| PERMUT ( 2, 2 ) | 2 |
| PERMUT ( 3, 1 ) | 3 |
| PERMUT ( 3, 2 ) | 6 |
| PERMUT ( 3, 3 ) | 6 |
| PERMUT ( 4, 1 ) | 4 |
| PERMUT ( 4, 2 ) | 12 |
| PERMUT ( 4, 3 ) | 24 |
| PERMUT ( 4, 4 ) | 24 |

## Related functions

Other related functions are:

- [[COMBIN]]
- [[COMBINA]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Poul Jørgensen, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/permut-function-dax](https://docs.microsoft.com/en-us/dax/permut-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
