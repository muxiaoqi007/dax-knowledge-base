---
title: "EXPON.DIST"
function: "expon-dist"
category: "Statistical"
url: "https://dax.guide/expon-dist/"
source: "dax.guide"
重要度:
难度:
---

# EXPON.DIST DAX Function (Statistical)

Returns the exponential distribution. Use EXPON.DIST to model the time between events, such as how long an automated bank teller takes to deliver cash. For example, you can use EXPON.DIST to determine the probability that the process takes at most 1 minute.

## Syntax

EXPON.DIST ( <X>, <Lambda>, <Cumulative> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| X |  | The value of the function. |
| Lambda |  | The parameter value. |
| Cumulative |  | A logical value that indicates which form of the exponential function to provide. If cumulative is TRUE, EXPON.DIST returns the cumulative distribution function; if FALSE, it returns the probability density function. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

## Examples

```dax


--  Given the lambda parameter to define the power, EXPON.DIST returns the 

--  exponential distribution of a value, or the cumulative distribution

--  depending on its third argument

--

--  https://en.wikipedia.org/wiki/Exponential_distribution

DEFINE

    VAR Vals       = GENERATESERIES ( 0, 5, 0.5 )

    VAR Lambda     = 0.8

    VAR CumulativeTrue  = TRUE   -- Cumulative distribution function

    VAR CumulativeFalse = FALSE  -- Probability density function

EVALUATE

ADDCOLUMNS ( 

    Vals, 

    "EXPON.DIST",  EXPON.DIST ( [Value], Lambda, CumulativeFalse ), 

    "EXPON.DIST Cumulative",  EXPON.DIST ( [Value], Lambda, CumulativeTrue )

)

```

| Value | EXPON.DIST | EXPON.DIST Cumulative |
| --- | --- | --- |
| 0.00 | 0.80 | 0.00 |
| 0.50 | 0.54 | 0.33 |
| 1.00 | 0.36 | 0.55 |
| 1.50 | 0.24 | 0.70 |
| 2.00 | 0.16 | 0.80 |
| 2.50 | 0.11 | 0.86 |
| 3.00 | 0.07 | 0.91 |
| 3.50 | 0.05 | 0.94 |
| 4.00 | 0.03 | 0.96 |
| 4.50 | 0.02 | 0.97 |
| 5.00 | 0.01 | 0.98 |

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/expon-dist-function-dax](https://docs.microsoft.com/en-us/dax/expon-dist-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
