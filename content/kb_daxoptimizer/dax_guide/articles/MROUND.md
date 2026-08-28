---
title: "MROUND"
function: "mround"
category: "Math and Trig"
url: "https://dax.guide/mround/"
source: "dax.guide"
重要度:
难度:
---

# MROUND DAX Function (Math and Trig)

Returns a number rounded to the desired multiple.

## Syntax

MROUND ( <Number>, <Multiple> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | The value to round. |
| Multiple |  | The multiple to which you want to round the number. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Rounded number.

## Remarks

MROUND rounds up, away from zero, if the remainder of dividing Number by the specified Multiple is greater than or equal to half the value of Multiple. However, when a decimal value is provided to the Multiple argument , the rounding direction is undefined for midpoint numbers. For example, MROUND ( 6.05, 0.1 ) returns 6.0 while MROUND ( 7.05, 0.1 ) returns 7.1.

The Number and Multiple arguments must have the same sign (+/-).

[» 2 related articles](#articles)  
[» 7 related functions](#alt)  

## Examples

Use MROUND ( number, SIGN ( number ) \* multiple ) if the the sign of the number is not known in advance:

```dax


DifferenceYOY RoundedToThousands := 

VAR Difference = [DifferenceYOY] 

VAR Multiple = 1000 

RETURN 

    MROUND ( Difference, SIGN ( Difference ) * multiple ) 

```

```dax


--  Rounding functions, using multiples of the second argument

--

--  FLOOR returns the smaller multiple

--  MROUND returns the nearer multiple (does not work with negative values)

--  CEILING returns the larger multiple

--  ISO.CEILING is like CEILING, handles differently negative numbers

DEFINE VAR Vals = GENERATESERIES ( 5, 20, 2 )

EVALUATE    

    ADDCOLUMNS (

        Vals,

        "FLOOR",        FLOOR       ( [Value], 3 ),

        "MROUND",       MROUND      ( [Value], 3 ),

        "CEILING",      CEILING     ( [Value], 3 ),

        "ISO.CEILING",  ISO.CEILING ( [Value], 3 )   

    )

```

| Value | FLOOR | MROUND | CEILING | ISO.CEILING |
| --- | --- | --- | --- | --- |
| 5 | 3 | 6 | 6 | 6 |
| 7 | 6 | 6 | 9 | 9 |
| 9 | 9 | 9 | 9 | 9 |
| 11 | 9 | 12 | 12 | 12 |
| 13 | 12 | 12 | 15 | 15 |
| 15 | 15 | 15 | 15 | 15 |
| 17 | 15 | 18 | 18 | 18 |
| 19 | 18 | 18 | 21 | 21 |

## Related articles

Learn more about MROUND in the following articles:

- [**Introducing the RANK window function in DAX**](https://www.sqlbi.com/articles/introducing-the-rank-window-function-in-dax/)

  RANK is a new DAX function to rank items based on multiple columns. This article introduces the RANK function and its differences with RANKX. [» Read more](https://www.sqlbi.com/articles/introducing-the-rank-window-function-in-dax/)
- [**Introducing RANKX in DAX**](https://www.sqlbi.com/articles/introducing-rankx-in-dax/)

  RANKX is a simple function used to rank a value within a list of values. Its use is simple, but it can be a source of frustration for newbies. In this article we introduce the RANKX function with a few examples. [» Read more](https://www.sqlbi.com/articles/introducing-rankx-in-dax/)

## Related functions

Other related functions are:

- [[ROUND]]
- [[ROUNDDOWN]]
- [[ROUNDUP]]
- [[FIXED]]
- [[CEILING]]
- [[FLOOR]]
- [[ISO.CEILING]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Jes Hansen

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/mround-function-dax](https://docs.microsoft.com/en-us/dax/mround-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
