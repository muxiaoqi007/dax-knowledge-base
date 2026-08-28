---
title: "T.DIST.RT"
function: "t-dist-rt"
category: "Statistical"
url: "https://dax.guide/t-dist-rt/"
source: "dax.guide"
重要度:
难度:
---

# T.DIST.RT DAX Function (Statistical)

Returns the right-tailed Student’s t-distribution.

## Syntax

T.DIST.RT ( <X>, <Deg\_freedom> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| X |  | The numeric value at which to evaluate the distribution. |
| Deg\_freedom |  | An integer indicating the number of degrees of freedom. |

## Return values

Scalar A single value of any type.

The right-tailed Student’s t-distribution.

## Remarks

The Deg\_freedom argument cannot be negative.

[» 4 related functions](#alt)  

## Examples

```dax


--  T.DIST    returns the Student's left-tailed t-distribution

--  T.DIST.2T returns the two-tailed Student's t-distribution

--  T.DIST.RT returns the right-tailed Student's t-distribution

--

--      T.DIST ( Value, DegFreedom, Cumulative )

--

--  https://en.wikipedia.org/wiki/Student%27s_t-distribution

DEFINE

    VAR Vals            = GENERATESERIES ( 0, 5, 0.25 )

    VAR DegFreedom      = 4.6

    VAR CumulativeTrue  = TRUE   -- Cumulative distribution function

    VAR CumulativeFalse = FALSE  -- Probability density function

EVALUATE

ADDCOLUMNS ( 

    Vals, 

    "T Dist",            -- Probability density function

        FORMAT ( 

            T.DIST ( [Value], DegFreedom, CumulativeFalse ),

            "#,0.0####"

        ),

    "T Dist Cumulative", -- Cumulative distribution function

        FORMAT ( 

            T.DIST ( [Value], DegFreedom, CumulativeTrue ),

            "#,0.0####"

        ),

    "2T Dist",            -- Probability density function

        FORMAT ( 

            T.DIST.2T ( [Value], DegFreedom ),

            "#,0.0####"

        ),

    "RT Dist", -- Cumulative distribution function

        FORMAT ( 

            T.DIST.RT ( [Value], DegFreedom ),

            "#,0.0####"

        )

    )

```

| Value | T Dist | T Dist Cumulative | 2T Dist | RT Dist |
| --- | --- | --- | --- | --- |
| 0.0000000000 | 0.37961 | 0.5 | 1.0 | 0.5 |
| 0.2500000000 | 0.36572 | 0.59373 | 0.81253 | 0.40627 |
| 0.5000000000 | 0.32792 | 0.68085 | 0.6383 | 0.31915 |
| 0.7500000000 | 0.2757 | 0.75649 | 0.48702 | 0.24351 |
| 1.0000000000 | 0.21968 | 0.81839 | 0.36322 | 0.18161 |
| 1.2500000000 | 0.16789 | 0.86669 | 0.26662 | 0.13331 |
| 1.5000000000 | 0.12452 | 0.90305 | 0.1939 | 0.09695 |
| 1.7500000000 | 0.09054 | 0.92974 | 0.14052 | 0.07026 |
| 2.0000000000 | 0.06509 | 0.94903 | 0.10194 | 0.05097 |
| 2.2500000000 | 0.04657 | 0.96286 | 0.07428 | 0.03714 |
| 2.5000000000 | 0.03333 | 0.97275 | 0.05449 | 0.02725 |
| 2.7500000000 | 0.02393 | 0.97984 | 0.04031 | 0.02016 |
| 3.0000000000 | 0.01729 | 0.98495 | 0.0301 | 0.01505 |
| 3.2500000000 | 0.01259 | 0.98865 | 0.0227 | 0.01135 |
| 3.5000000000 | 0.00924 | 0.99136 | 0.01728 | 0.00864 |
| 3.7500000000 | 0.00685 | 0.99335 | 0.01329 | 0.00665 |
| 4.0000000000 | 0.00512 | 0.99484 | 0.01032 | 0.00516 |
| 4.2500000000 | 0.00387 | 0.99595 | 0.00809 | 0.00405 |
| 4.5000000000 | 0.00295 | 0.9968 | 0.0064 | 0.0032 |
| 4.7500000000 | 0.00227 | 0.99745 | 0.0051 | 0.00255 |
| 5.0000000000 | 0.00176 | 0.99795 | 0.0041 | 0.00205 |

## Related functions

Other related functions are:

- [[T.DIST]]
- [[T.DIST.2T]]
- [[T.INV]]
- [[T.INV.2T]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/t-dist-rt-dax](https://docs.microsoft.com/en-us/dax/t-dist-rt-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
