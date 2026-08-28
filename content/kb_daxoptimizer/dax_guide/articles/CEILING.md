---
title: "CEILING"
function: "ceiling"
category: "Math and Trig"
url: "https://dax.guide/ceiling/"
source: "dax.guide"
重要度:
难度:
---

# CEILING DAX Function (Math and Trig)

Rounds a number up, to the nearest integer or to the nearest unit of significance.

## Syntax

CEILING ( <Number>, <Significance> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | The value you want to round. |
| Significance |  | The multiple to which you want to round. |

## Return values

Scalar A single value of one these types: [integer](https://dax.guide/dt/integer), [decimal](https://dax.guide/dt/decimal), [currency](https://dax.guide/dt/currency).

The number is rounded as specified. The return data type is usually of the same type of the significant argument, with the following exceptions:

- If the number argument type is Currency, the return type is Currency.
- If the significance argument type is Boolean, the return type is Integer.
- If the significance argument type is non-numeric, the return type is Decimal.

## Remarks

There are two CEILING functions in DAX, with the following differences:

- The CEILING function emulates the behavior of the CEILING function in Excel.
- The [ISO.CEILING](https://dax.guide/iso.ceiling/) function follows the ISO-defined behavior for determining the ceiling value.

The two functions return the same value for positive numbers, but different values for negative numbers. When using a positive multiple of significance, both CEILING and [ISO.CEILING](https://dax.guide/iso.ceiling/) round negative numbers upward (toward positive infinity). When using a negative multiple of significance, CEILING rounds negative numbers downward (toward negative infinity), while [ISO.CEILING](https://dax.guide/iso.ceiling/) rounds negative numbers upward (toward positive infinity).

[» 7 related functions](#alt)  

## Examples

```dax


= CEILING  ( 10.2, 1 )                   -- Returns 11     (Integer)

= CEILING  ( 10.7, 1 )                   -- Returns 11     (Integer)

= CEILING  ( 10.2, 0.5 )                 -- Returns 10.5   (Decimal)

= CEILING  ( 10.7, 0.5 )                 -- Returns 11     (Decimal)

= CEILING  ( 10.2, CURRENCY ( 0.5 ) )    -- Returns 10.5   (Currency)

= CEILING  ( 10.7, CURRENCY ( 0.5 ) )    -- Returns 11     (Currency)

= CEILING  ( -10.2, 1 )                  -- Returns -10    (Integer)

= CEILING  ( -10.2, -1 )                 -- Returns -11    (Integer)

= CEILING  ( -10.7, 1 )                  -- Returns -10    (Integer)

= CEILING  ( -10.7, -1 )                 -- Returns -11    (Integer)

= CEILING  ( -10.2, 0.5 )                -- Returns -10    (Decimal)

= CEILING  ( -10.7, 0.5 )                -- Returns -10.5  (Decimal)

= CEILING  ( -10.2, CURRENCY ( 0.5 ) )   -- Returns -10    (Currency)

= CEILING  ( -10.7, CURRENCY ( 0.5 ) )   -- Returns -10.5  (Currency)

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

## Related functions

Other related functions are:

- [[ISO.CEILING]]
- [[FIXED]]
- [[FLOOR]]
- [[MROUND]]
- [[ROUND]]
- [[ROUNDDOWN]]
- [[ROUNDUP]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/ceiling-function-dax](https://docs.microsoft.com/en-us/dax/ceiling-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
