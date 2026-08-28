---
title: "RANDBETWEEN"
function: "randbetween"
category: "Math and Trig"
url: "https://dax.guide/randbetween/"
source: "dax.guide"
重要度:
难度:
---

# RANDBETWEEN DAX Function (Math and Trig) Volatile

Returns a random number between the numbers you specify.

## Syntax

RANDBETWEEN ( <Bottom>, <Top> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Bottom |  | The smallest integer RANDBETWEEN will return. |
| Top |  | The largest integer RANDBETWEEN will return. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

A random integer number between Bottom and Top, which are included in the range of the possible integer results.  
If Bottom is greater than Top, then RANDSBETWEEN raises an error.

[» 1 related function](#alt)  

## Examples

```dax


--  RAND returns a random number between 0 and 1

--  RANDBETWEEN returns a random number between 

--  the two provide boundaries

DEFINE 

    VAR Vals = GENERATESERIES ( 1, 10, 1 )

VAR Random = 

    ADDCOLUMNS ( 

        Vals,

        "RAND",        RAND (),

        "RANDBETWEEN", RANDBETWEEN (-[Value], +[Value] )

    )

EVALUATE

    Random

EVALUATE

    {

        ( "Random Average (around 0.5)",  AVERAGEX ( Random, [RAND] ) ),

        ( "RandBetween Avg (near 0)",      AVERAGEX ( Random, [RANDBETWEEN] ) ),

        ( "RandBetween Min (near -10000)", MINX ( Random, [RANDBETWEEN] ) ),

        ( "RandBetween Max (near  10000)", MAXX ( Random, [RANDBETWEEN] ) )

    }

```

| Value | RAND | RANDBETWEEN |
| --- | --- | --- |
| 1 | 0.31 | 0 |
| 2 | 0.87 | 2 |
| 3 | 0.54 | 0 |
| 4 | 0.91 | 1 |
| 5 | 0.24 | 3 |
| 6 | 0.40 | 2 |
| 7 | 0.39 | -6 |
| 8 | 0.40 | 6 |
| 9 | 0.99 | -6 |
| 10 | 0.41 | 1 |

| Value1 | Value2 |
| --- | --- |
| Random Average (around 0.5) | 0.55 |
| RandBetween Avg (near 0) | 0.30 |
| RandBetween Min (near -10000) | -6.00 |
| RandBetween Max (near 10000) | 6.00 |

## Related functions

Other related functions are:

- [[RAND]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/randbetween-function-dax](https://docs.microsoft.com/en-us/dax/randbetween-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
