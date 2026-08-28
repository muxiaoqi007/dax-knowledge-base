---
title: "POISSON.DIST"
function: "poisson-dist"
category: "Statistical"
url: "https://dax.guide/poisson-dist/"
source: "dax.guide"
重要度:
难度:
---

# POISSON.DIST DAX Function (Statistical)

Returns the Poisson distribution. A common application of the Poisson distribution is predicting the number of events over a specific time, such as the number of cars arriving at a toll plaza in 1 minute.

## Syntax

POISSON.DIST ( <X>, <Mean>, <Cumulative> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| X |  | The number of events. |
| Mean |  | The expected numeric value. |
| Cumulative |  | A logical value that determines the form of the probability distribution returned. If cumulative is TRUE, POISSON.DIST returns the cumulative Poisson probability that the number of random events occurring will be between zero and x inclusive; if FALSE, it returns the Poisson probability mass function that the number of events occurring will be exactly x. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Returns the Poisson distribution.

## Remarks

If x is not an integer, it is rounded.

If x or mean is nonnumeric, POISSON.DIST returns the #[[VALUE]]! error value.

If x < 0, POISSON.DIST returns the #NUM! error value.
If mean < 0, POISSON.DIST returns the #NUM! error value.

## Examples

```dax


--  POISSON.DIST returns the Poisson distribution 

--  Use case: predicting the number of events over

--  a specific time, such as the number of customers

--  entering a store in 1 hour

--      POISSON.DIST ( Value, Mean, Cumulative )

DEFINE

    VAR Mean            = 10 -- Expected number of customers in 1 hour

    VAR Vals            = GENERATESERIES ( 0, 20, 1 )

    VAR CumulativeTrue  = TRUE   -- Cumulative distribution function

    VAR CumulativeFalse = FALSE  -- Probability density function

EVALUATE

ADDCOLUMNS ( 

    Vals, 

    "Poisson Distr.",             -- Probability density function

        FORMAT ( 

            POISSON.DIST ( [Value], Mean, CumulativeFalse ),

            "#,0.00000"

        ),

    "Poisson Distr. Cumulative",  -- Cumulative distribution function

        FORMAT ( 

            POISSON.DIST ( [Value], Mean, CumulativeTrue ),

            "#,0.00000"

        )

)

```

| Value | Poisson Distr. | Poisson Distr. Cumulative |
| --- | --- | --- |
| 0 | 0.00005 | 0.00005 |
| 1 | 0.00045 | 0.00050 |
| 2 | 0.00227 | 0.00277 |
| 3 | 0.00757 | 0.01034 |
| 4 | 0.01892 | 0.02925 |
| 5 | 0.03783 | 0.06709 |
| 6 | 0.06306 | 0.13014 |
| 7 | 0.09008 | 0.22022 |
| 8 | 0.11260 | 0.33282 |
| 9 | 0.12511 | 0.45793 |
| 10 | 0.12511 | 0.58304 |
| 11 | 0.11374 | 0.69678 |
| 12 | 0.09478 | 0.79156 |
| 13 | 0.07291 | 0.86446 |
| 14 | 0.05208 | 0.91654 |
| 15 | 0.03472 | 0.95126 |
| 16 | 0.02170 | 0.97296 |
| 17 | 0.01276 | 0.98572 |
| 18 | 0.00709 | 0.99281 |
| 19 | 0.00373 | 0.99655 |
| 20 | 0.00187 | 0.99841 |

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/poisson-dist-function-dax](https://docs.microsoft.com/en-us/dax/poisson-dist-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
