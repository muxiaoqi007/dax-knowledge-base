---
title: "NORM.INV"
function: "norm-inv"
category: "Statistical"
url: "https://dax.guide/norm-inv/"
source: "dax.guide"
重要度:
难度:
---

# NORM.INV DAX Function (Statistical)

Returns the inverse of the normal cumulative distribution for the specified mean and standard deviation.

## Syntax

NORM.INV ( <Probability>, <Mean>, <Standard\_dev> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Probability |  | A probability corresponding to the normal distribution. |
| Mean |  | The arithmetic mean of the distribution. |
| Standard\_dev |  | The standard deviation of the distribution. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Returns the inverse of the normal cumulative distribution for the specified mean and standard deviation.

[» 3 related functions](#alt)  

## Examples

```dax


--  NORM.INV returns the inverse of the normal distribution 

--  for the specified mean and standard deviation

--

--  NORM.S.INV returns the inverse of the standard normal distribution 

--  (has a mean of zero and a standar deviation of one)

--

--  https://en.wikipedia.org/wiki/Normal_distribution

DEFINE

    TABLE SampleData    = { 2, 4, 4, 4, 5, 5, 7, 9 }

    VAR Mean            = AVERAGE ( SampleData[Value] )

    VAR StandardDev     = STDEV.S ( SampleData[Value] )

    VAR Percs           = GENERATESERIES ( 0.05, 0.99, 0.05 )

    VAR CumulativeTrue  = TRUE   -- Cumulative distribution function

    VAR CumulativeFalse = FALSE  -- Probability density function

EVALUATE

SELECTCOLUMNS (

    Percs,

    "Probability %", [Value],

    "Inv. Normal Distr.",          -- Probability density function

        FORMAT (

            NORM.INV ( [Value], Mean, StandardDev ),

            "#,0.00000"

        ),

    "Inv. Standard Normal Distr.", -- Probability density function

        FORMAT (

            NORM.S.INV ( [Value] ),

            "#,0.00000"

        )

)

```

| Probability % | Inv. Normal Distr. | Inv. Standard Normal Distr. |
| --- | --- | --- |
| 5.00% | 1.48316 | -1.64485 |
| 10.00% | 2.25993 | -1.28155 |
| 15.00% | 2.78401 | -1.03643 |
| 20.00% | 3.20054 | -0.84162 |
| 25.00% | 3.55788 | -0.67449 |
| 30.00% | 3.87878 | -0.52440 |
| 35.00% | 4.17615 | -0.38532 |
| 40.00% | 4.45832 | -0.25335 |
| 45.00% | 4.73132 | -0.12566 |
| 50.00% | 5.00000 | 0.00000 |
| 55.00% | 5.26868 | 0.12566 |
| 60.00% | 5.54168 | 0.25335 |
| 65.00% | 5.82385 | 0.38532 |
| 70.00% | 6.12122 | 0.52440 |
| 75.00% | 6.44212 | 0.67449 |
| 80.00% | 6.79946 | 0.84162 |
| 85.00% | 7.21599 | 1.03643 |
| 90.00% | 7.74007 | 1.28155 |
| 95.00% | 8.51684 | 1.64485 |

```dax


--  Example of how to use NORM.INV with RAND to generate Gaussian 

--  (normally distributed) random samples

DEFINE

    -- Mean

    VAR mu = 10 

    -- Standard deviation

    VAR sigma = 3 

    -- Amount of samples

    VAR N = 1000

    -- Table containing normally distributed random samples with the given parameters

    TABLE RawData =

        ADDCOLUMNS (

            GENERATESERIES ( 1, N ),

            "Random data", ROUND ( NORM.INV ( RAND (), mu, sigma ), 0 )

        )

    -- Table containing the "bins" which are used to summarize the random data

    TABLE SummaryTable =

        GENERATESERIES ( 1, 20 )



EVALUATE

ADDCOLUMNS (

    SummaryTable,

    "Count of occurrences",

        -- Caculate the amount of samples per category of the summarize table

        CALCULATE (

            COUNTROWS ( RawData ),

            FILTER ( RawData, RawData[Random data] = SummaryTable[Value] )

        ),

    "Histogram",

        -- Form a visual histogram of the results

        CONCATENATEX (

            -- Form a temporary table for creating the histogram via string concatenation

            ADDCOLUMNS (

                FILTER ( RawData, RawData[Random data] = SummaryTable[Value] ),

                "|", "|"

            ),

            "|"

        )

)

```

| SummaryTable[Value] | Count of occurrences | Histogram |
| --- | --- | --- |
| 1 | (Blank) | (Blank) |
| 2 | 4 | |||| |
| 3 | 8 | |||||||| |
| 4 | 16 | |||||||||||||||| |
| 5 | 32 | |||||||||||||||||||||||||||||||| |
| 6 | 50 | |||||||||||||||||||||||||||||||||||||||||||||||||| |
| 7 | 81 | ||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||| |
| 8 | 116 | |||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||| |
| 9 | 130 | |||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||| |
| 10 | 140 | |||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||| |
| 11 | 126 | |||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||| |
| 12 | 87 | ||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||| |
| 13 | 78 | |||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||| |
| 14 | 65 | ||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||| |
| 15 | 29 | ||||||||||||||||||||||||||||| |
| 16 | 21 | ||||||||||||||||||||| |
| 17 | 12 | |||||||||||| |
| 18 | 3 | ||| |
| 19 | 1 | | |
| 20 | (Blank) | (Blank) |

## Related functions

Other related functions are:

- [[NORM.DIST]]
- [[NORM.S.DIST]]
- [[NORM.S.INV]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Ville-Pietari Louhiala

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/norm-inv-dax](https://docs.microsoft.com/en-us/dax/norm-inv-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
