---
title: "NORM.S.DIST"
function: "norm-s-dist"
category: "Statistical"
url: "https://dax.guide/norm-s-dist/"
source: "dax.guide"
重要度:
难度:
---

# NORM.S.DIST DAX Function (Statistical)

Returns the standard normal distribution (has a mean of zero and a standard deviation of one).

## Syntax

NORM.S.DIST ( <Z>, <Cumulative> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Z |  | The value for which you want the distribution. |
| Cumulative |  | Cumulative is a logical value that determines the form of the function. If cumulative is TRUE, NORMS.DIST returns the cumulative distribution function; if FALSE, it returns the probability mass function. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

The standard normal distribution (has a mean of zero and a standard deviation of one.

[» 3 related functions](#alt)  

## Examples

```dax


--  NORM.DIST returns the normal distribution for the 

--  specified mean and standard deviation

--

--      NORM.DIST ( Value, Mean, StandardDev, Cumulative )

--

--  NORM.S.DIST returns the standard normal distribution

--  (has a mean of zero and a standard deviation of one)

--

--  https://en.wikipedia.org/wiki/Normal_distribution

DEFINE

    TABLE SampleData    = { 2, 4, 4, 4, 5, 5, 7, 9 }

    VAR Mean            = AVERAGE ( SampleData[Value] )

    VAR StandardDev     = STDEV.S ( SampleData[Value] )

    VAR Vals            = GENERATESERIES ( 0.0, 10, 0.5 )

    VAR CumulativeTrue  = TRUE   -- Cumulative distribution function

    VAR CumulativeFalse = FALSE  -- Probability density function

EVALUATE

ADDCOLUMNS ( 

    Vals, 

    "Normal Distr.",             -- Probability density function

        FORMAT ( 

            NORM.DIST ( [Value], Mean, StandardDev, CumulativeFalse ),

            "#,0.00000"

        ),

    "Normal Distr. Cumulative",  -- Cumulative distribution function

        FORMAT ( 

            NORM.DIST ( [Value], Mean, StandardDev, CumulativeTrue ),

            "#,0.00000"

        ),

    "Normal Std. Distr.",        -- Cumulative distribution function

        FORMAT ( 

            NORM.S.DIST ( [Value], CumulativeFalse ),

            "#,0.00000"

        ),

    "Normal Std. Distr. Cumulative", -- Cumulative distribution function

        FORMAT ( 

            NORM.S.DIST ( [Value], CumulativeTrue ),

            "#,0.00000"

        )        

)

```

| Value | Normal Distr. | Normal Distr. Cumulative | Normal Std. Distr. | Normal Std. Distr. Cumulative |
| --- | --- | --- | --- | --- |
| 0.00 | 0.01212 | 0.00968 | 0.39894 | 0.50000 |
| 0.50 | 0.02037 | 0.01766 | 0.35207 | 0.69146 |
| 1.00 | 0.03242 | 0.03068 | 0.24197 | 0.84134 |
| 1.50 | 0.04886 | 0.05082 | 0.12952 | 0.93319 |
| 2.00 | 0.06972 | 0.08029 | 0.05399 | 0.97725 |
| 2.50 | 0.09419 | 0.12115 | 0.01753 | 0.99379 |
| 3.00 | 0.12047 | 0.17479 | 0.00443 | 0.99865 |
| 3.50 | 0.14588 | 0.24148 | 0.00087 | 0.99977 |
| 4.00 | 0.16726 | 0.32000 | 0.00013 | 0.99997 |
| 4.50 | 0.18156 | 0.40755 | 0.00002 | 1.00000 |
| 5.00 | 0.18659 | 0.50000 | 0.00000 | 1.00000 |
| 5.50 | 0.18156 | 0.59245 | 0.00000 | 1.00000 |
| 6.00 | 0.16726 | 0.68000 | 0.00000 | 1.00000 |
| 6.50 | 0.14588 | 0.75852 | 0.00000 | 1.00000 |
| 7.00 | 0.12047 | 0.82521 | 0.00000 | 1.00000 |
| 7.50 | 0.09419 | 0.87885 | 0.00000 | 1.00000 |
| 8.00 | 0.06972 | 0.91971 | 0.00000 | 1.00000 |
| 8.50 | 0.04886 | 0.94918 | 0.00000 | 1.00000 |
| 9.00 | 0.03242 | 0.96932 | 0.00000 | 1.00000 |
| 9.50 | 0.02037 | 0.98234 | 0.00000 | 1.00000 |
| 10.00 | 0.01212 | 0.99032 | 0.00000 | 1.00000 |

## Related functions

Other related functions are:

- [[NORM.DIST]]
- [[NORM.INV]]
- [[NORM.S.INV]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/norm-s-dist-dax](https://docs.microsoft.com/en-us/dax/norm-s-dist-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
