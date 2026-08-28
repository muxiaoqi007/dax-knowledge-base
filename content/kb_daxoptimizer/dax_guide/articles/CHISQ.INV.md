---
title: "CHISQ.INV"
function: "chisq-inv"
category: "Statistical"
url: "https://dax.guide/chisq-inv/"
source: "dax.guide"
重要度:
难度:
---

# CHISQ.INV DAX Function (Statistical)

Returns the inverse of the left-tailed probability of the chi-squared distribution. The chi-squared distribution is commonly used to study variation in the percentage of something across samples, such as the fraction of the day people spend watching television.

## Syntax

CHISQ.INV ( <Probability>, <Deg\_freedom> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Probability |  | A probability associated with the chi-squared distribution. |
| Deg\_freedom |  | The number of degrees of freedom. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Returns the inverse of the left-tailed probability of the chi-squared distribution.

## Remarks

If argument is nonnumeric, CHISQ.INV returns the #[[VALUE]]! error value.  
If probability < 0 or probability > 1, CHISQ.INV returns the #NUM! error value.  
If deg\_freedom is not an integer, it is rounded.  
If deg\_freedom < 1 or deg\_freedom > 10^10, CHISQ.INV returns the #NUM! error value.

[» 3 related functions](#alt)  

## Examples

```dax


--  Given the degrees of freedom, CHISQ.DIST returns the value

--  of the probability density function using the chi-square

--  distribution, or the cumulative distribution depending on the

--  third argument.

--

--      CHISQ.DIST ( [Value], DegFreedom, Cumulative )

--

--  The .INV functions provide the inverse calculation

--  The .RT functions return the right tail of the chi-square, they

--  do not need the Cumulative argument, which is always TRUE

--  CHISQ.DIST.RT = (1 - CHISQ.DIST)

--

--  https://en.wikipedia.org/wiki/Chi-square_distribution

DEFINE

    VAR Vals            = GENERATESERIES ( 0, 10, 0.28 )

    VAR DegFreedom      = 5

    VAR CumulativeTrue  = TRUE   -- Cumulative distribution function

    VAR CumulativeFalse = FALSE  -- Probability density function

EVALUATE

ADDCOLUMNS ( 

    Vals, 

    "ChiSquare Dist",            -- Probability density function

        FORMAT ( 

            CHISQ.DIST ( [Value], DegFreedom, CumulativeFalse ),

            "#,0.0####"

        ),

    "ChiSquare Dist Cumulative", -- Cumulative distribution function

        FORMAT ( 

            CHISQ.DIST ( [Value], DegFreedom, CumulativeTrue ),

            "#,0.0####"

        ),

    "ChiSquare Inv Cumulative",

        CHISQ.INV (  

            CHISQ.DIST ( [Value], DegFreedom, CumulativeTrue ),

            DegFreedom

        ),

    "ChiSquare Dist RT", 

        FORMAT ( 

            CHISQ.DIST.RT ( [Value], DegFreedom ),

            "#,0.0####"

        ),

    "ChiSquare RT Inv",

        CHISQ.INV.RT (  

            CHISQ.DIST.RT ( [Value], DegFreedom ),

            DegFreedom

        )

    )

```

| Value | ChiSquare Dist | ChiSquare Dist Cumulative | ChiSquare Inv Cumulative | ChiSquare Dist RT | ChiSquare RT Inv |
| --- | --- | --- | --- | --- | --- |
| 0.00 | 0.0 | 0.0 | 0.00 | 1.0 | 0.00 |
| 0.28 | 0.01713 | 0.002 | 0.28 | 0.998 | 0.28 |
| 0.56 | 0.04212 | 0.01024 | 0.56 | 0.98976 | 0.56 |
| 0.84 | 0.06727 | 0.02559 | 0.84 | 0.97441 | 0.84 |
| 1.12 | 0.09003 | 0.04768 | 1.12 | 0.95232 | 1.12 |
| 1.40 | 0.10939 | 0.07569 | 1.40 | 0.92431 | 1.40 |
| 1.68 | 0.12501 | 0.10859 | 1.68 | 0.89141 | 1.68 |
| 1.96 | 0.13695 | 0.14535 | 1.96 | 0.85465 | 1.96 |
| 2.24 | 0.14546 | 0.18496 | 2.24 | 0.81504 | 2.24 |
| 2.52 | 0.1509 | 0.22652 | 2.52 | 0.77348 | 2.52 |
| 2.80 | 0.15364 | 0.26921 | 2.80 | 0.73079 | 2.80 |
| 3.08 | 0.1541 | 0.31235 | 3.08 | 0.68765 | 3.08 |
| 3.36 | 0.15265 | 0.35533 | 3.36 | 0.64467 | 3.36 |
| 3.64 | 0.14963 | 0.39768 | 3.64 | 0.60232 | 3.64 |
| 3.92 | 0.14538 | 0.43901 | 3.92 | 0.56099 | 3.92 |
| 4.20 | 0.14017 | 0.47901 | 4.20 | 0.52099 | 4.20 |
| 4.48 | 0.13424 | 0.51744 | 4.48 | 0.48256 | 4.48 |
| 4.76 | 0.12781 | 0.55413 | 4.76 | 0.44587 | 4.76 |
| 5.04 | 0.12106 | 0.58898 | 5.04 | 0.41102 | 5.04 |
| 5.32 | 0.11414 | 0.62191 | 5.32 | 0.37809 | 5.32 |
| 5.60 | 0.10716 | 0.65289 | 5.60 | 0.34711 | 5.60 |
| 5.88 | 0.10024 | 0.68193 | 5.88 | 0.31807 | 5.88 |
| 6.16 | 0.09344 | 0.70904 | 6.16 | 0.29096 | 6.16 |
| 6.44 | 0.08683 | 0.73427 | 6.44 | 0.26573 | 6.44 |
| 6.72 | 0.08047 | 0.75769 | 6.72 | 0.24231 | 6.72 |
| 7.00 | 0.07437 | 0.77936 | 7.00 | 0.22064 | 7.00 |
| 7.28 | 0.06857 | 0.79936 | 7.28 | 0.20064 | 7.28 |
| 7.56 | 0.06309 | 0.81779 | 7.56 | 0.18221 | 7.56 |
| 7.84 | 0.05792 | 0.83472 | 7.84 | 0.16528 | 7.84 |
| 8.12 | 0.05307 | 0.85025 | 8.12 | 0.14975 | 8.12 |
| 8.40 | 0.04855 | 0.86447 | 8.40 | 0.13553 | 8.40 |
| 8.68 | 0.04433 | 0.87747 | 8.68 | 0.12253 | 8.68 |
| 8.96 | 0.04042 | 0.88933 | 8.96 | 0.11067 | 8.96 |
| 9.24 | 0.0368 | 0.90013 | 9.24 | 0.09987 | 9.24 |
| 9.52 | 0.03346 | 0.90996 | 9.52 | 0.09004 | 9.52 |
| 9.80 | 0.03038 | 0.9189 | 9.80 | 0.0811 | 9.80 |

## Related functions

Other related functions are:

- [[CHISQ.DIST]]
- [[CHISQ.DIST.RT]]
- [[CHISQ.INV.RT]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/chisq-inv-function-dax](https://docs.microsoft.com/en-us/dax/chisq-inv-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
