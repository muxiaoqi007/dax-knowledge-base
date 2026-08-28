---
title: "FLOOR"
function: "floor"
category: "Math and Trig"
url: "https://dax.guide/floor/"
source: "dax.guide"
重要度:
难度:
---

# FLOOR DAX Function (Math and Trig)

Rounds a number down, toward zero, to the nearest multiple of significance.

## Syntax

FLOOR ( <Number>, <Significance> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | The number you want to round. |
| Significance |  | The multiple to which you want to round. Number and significance must either both be positive or both be negative. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Number down to requested significance

## Remarks

The functions always returns a decimal data type, regardless of the arguments.

[» 1 related article](#articles)  
[» 7 related functions](#alt)  

## Examples

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

```dax


--  When the significance is negative, CEILING and ISO.CEILING

--  behave differently

--  

--  CEILING rounds towards the smaller value ISO.CEILING rounds towards

--  the largest one. This is important with negative significance

DEFINE

    VAR Vals = GENERATESERIES ( -20, 0, 3 )

    VAR Significance = -3

EVALUATE

    ADDCOLUMNS (

        Vals,

        "FLOOR",        FLOOR       ( [Value], Significance ),

        "CEILING",      CEILING     ( [Value], Significance ),

        "ISO.CEILING",  ISO.CEILING ( [Value], Significance )

    )



```

| Value | FLOOR | CEILING | ISO.CEILING |
| --- | --- | --- | --- |
| -20 | -18 | -21 | -18 |
| -17 | -15 | -18 | -15 |
| -14 | -12 | -15 | -12 |
| -11 | -9 | -12 | -9 |
| -8 | -6 | -9 | -6 |
| -5 | -3 | -6 | -3 |
| -2 | 0 | -3 | 0 |

## Related articles

Learn more about FLOOR in the following articles:

- [**Highlighting a data point and comparing to all others in a distribution**](https://www.sqlbi.com/articles/highlighting-a-data-point-and-comparing-to-all-others-in-a-distribution/)

  This article shows how you can highlight a single value in visual when comparing the distribution of a measure for one value versus all others. [» Read more](https://www.sqlbi.com/articles/highlighting-a-data-point-and-comparing-to-all-others-in-a-distribution/)

## Related functions

Other related functions are:

- [[CEILING]]
- [[FIXED]]
- [[ISO.CEILING]]
- [[MROUND]]
- [[ROUND]]
- [[ROUNDDOWN]]
- [[ROUNDUP]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/floor-function-dax](https://docs.microsoft.com/en-us/dax/floor-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
