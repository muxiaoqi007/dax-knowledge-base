---
title: "BETA.DIST"
function: "beta-dist"
category: "Statistical"
url: "https://dax.guide/beta-dist/"
source: "dax.guide"
重要度:
难度:
---

# BETA.DIST DAX Function (Statistical)

Returns the beta distribution. The beta distribution is commonly used to study variation in the percentage of something across samples, such as the fraction of the day people spend watching television.

## Syntax

BETA.DIST ( <X>, <Alpha>, <Beta>, <Cumulative> [, <A>] [, <B>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| X |  | The value between A and B at which to evaluate the function. |
| Alpha |  | A parameter of the distribution. |
| Beta |  | A parameter the distribution. |
| Cumulative |  | A logical value that determines the form of the function. If cumulative is TRUE, BETA.DIST returns the cumulative distribution function; if FALSE, it returns the probability density function. |
| A | Optional | A lower bound to the interval of x. |
| B | Optional | An upper bound to the interval of x. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Returns the beta distribution.

## Remarks

If any argument is nonnumeric, BETA.DIST returns the #[[VALUE]]! error value.

If any argument is not an integer, it is rounded.

If alpha ≤ 0 or beta ≤ 0, BETA.DIST returns the #NUM! error value.

If x < A, x > B, or A = B, BETA.DIST returns the #NUM! error value.

If you omit values for A and B, BETA.DIST uses the standard cumulative beta distribution, so that A = 0 and B = 1.

[» 1 related function](#alt)  

## Examples

```dax


--

--  Given the parameters of Alpha and Beta to define the shape of

--  the distribution, BETA.DIST returns the value of the probability

--  density function or the cumulative distribution function, depending

--  on the fourth argument.

--

--      BETA.DIST ( [Value], Alpha, Beta, Cumulative, LowerBound, UpperBound )

--

--  BETA.INV returns the inverse of BETA.DIST

--

--  https://en.wikipedia.org/wiki/Beta_distribution

--

DEFINE

    VAR Vals            = GENERATESERIES ( 0.01, 1, 0.02 )

    VAR Alpha           = 3.51

    VAR Beta            = 4.02

    VAR CumulativeTrue  = TRUE   -- Cumulative distribution function

    VAR CumulativeFalse = FALSE  -- Probability density function

    VAR LowerBound      = 0

    VAR UpperBound      = 1

EVALUATE

ADDCOLUMNS ( 

    Vals, 

    "Beta Dist",             -- Probability density function

        FORMAT ( 

            BETA.DIST ( [Value], Alpha, Beta, CumulativeFalse, LowerBound, UpperBound ),

            "#,0.0####"

        ),

    "Beta Dist Cumulative",  -- Cumulative distribution function

        FORMAT ( 

            BETA.DIST ( [Value], Alpha, Beta, CumulativeTrue, LowerBound, UpperBound ),

            "#,0.0####"

        ),

    "Beta Inv Cumulative",

        BETA.INV (  

            BETA.DIST ( [Value], Alpha, Beta, CumulativeTrue, LowerBound, UpperBound ),

            Alpha,

            Beta,

            LowerBound,

            UpperBound 

        )           

    )

```

| Value | Beta Dist | Beta Dist Cumulative | Beta Inv Cumulative |
| --- | --- | --- | --- |
| 0.01 | 0.00089 | 0.0 | 0.01 |
| 0.03 | 0.01317 | 0.00011 | 0.03 |
| 0.05 | 0.04459 | 0.00066 | 0.05 |
| 0.07 | 0.09729 | 0.00204 | 0.07 |
| 0.09 | 0.17121 | 0.00469 | 0.09 |
| 0.11 | 0.26492 | 0.00902 | 0.11 |
| 0.13 | 0.37619 | 0.01541 | 0.13 |
| 0.15 | 0.50222 | 0.02417 | 0.15 |
| 0.17 | 0.63989 | 0.03557 | 0.17 |
| 0.19 | 0.78589 | 0.04982 | 0.19 |
| 0.21 | 0.93685 | 0.06704 | 0.21 |
| 0.23 | 1.08944 | 0.0873 | 0.23 |
| 0.25 | 1.24045 | 0.11061 | 0.25 |
| 0.27 | 1.38683 | 0.13689 | 0.27 |
| 0.29 | 1.52575 | 0.16603 | 0.29 |
| 0.31 | 1.65465 | 0.19785 | 0.31 |
| 0.33 | 1.77125 | 0.23213 | 0.33 |
| 0.35 | 1.87358 | 0.26861 | 0.35 |
| 0.37 | 1.96001 | 0.30697 | 0.37 |
| 0.39 | 2.02923 | 0.34689 | 0.39 |
| 0.41 | 2.08029 | 0.38802 | 0.41 |
| 0.43 | 2.11257 | 0.42998 | 0.43 |
| 0.45 | 2.1258 | 0.4724 | 0.45 |
| 0.47 | 2.12003 | 0.51489 | 0.47 |
| 0.49 | 2.09565 | 0.55707 | 0.49 |
| 0.51 | 2.05332 | 0.59859 | 0.51 |
| 0.53 | 1.99403 | 0.63909 | 0.53 |
| 0.55 | 1.919 | 0.67825 | 0.55 |
| 0.57 | 1.82972 | 0.71576 | 0.57 |
| 0.59 | 1.72786 | 0.75135 | 0.59 |
| 0.61 | 1.61531 | 0.7848 | 0.61 |
| 0.63 | 1.49409 | 0.81591 | 0.63 |
| 0.65 | 1.36635 | 0.84452 | 0.65 |
| 0.67 | 1.23431 | 0.87053 | 0.67 |
| 0.69 | 1.10024 | 0.89388 | 0.69 |
| 0.71 | 0.96642 | 0.91454 | 0.71 |
| 0.73 | 0.83507 | 0.93255 | 0.73 |
| 0.75 | 0.70835 | 0.94798 | 0.75 |
| 0.77 | 0.58827 | 0.96093 | 0.77 |
| 0.79 | 0.47666 | 0.97157 | 0.79 |
| 0.81 | 0.37514 | 0.98007 | 0.81 |
| 0.83 | 0.28504 | 0.98665 | 0.83 |
| 0.85 | 0.20735 | 0.99155 | 0.85 |
| 0.87 | 0.14268 | 0.99503 | 0.87 |
| 0.89 | 0.09121 | 0.99735 | 0.89 |
| 0.91 | 0.05261 | 0.99876 | 0.91 |
| 0.93 | 0.02601 | 0.99953 | 0.93 |
| 0.95 | 0.00993 | 0.99987 | 0.95 |
| 0.97 | 0.00224 | 0.99998 | 0.97 |
| 0.99 | 0.00009 | 1.0 | 0.99 |

## Related functions

Other related functions are:

- [[BETA.DIST]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/beta-dist-function-dax](https://docs.microsoft.com/en-us/dax/beta-dist-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
