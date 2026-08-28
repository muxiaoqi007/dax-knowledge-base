---
title: "CONFIDENCE.NORM"
function: "confidence-norm"
category: "Statistical"
url: "https://dax.guide/confidence-norm/"
source: "dax.guide"
重要度:
难度:
---

# CONFIDENCE.NORM DAX Function (Statistical)

Returns the confidence interval for a population mean, using a normal distribution.

## Syntax

CONFIDENCE.NORM ( <Alpha>, <Standard\_dev>, <Size> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Alpha |  | The significance level used to compute the confidence level. The confidence level equals 100\*(1 – alpha)%, or in other words, an alpha of 0.05 indicates a 95 percent confidence level. |
| Standard\_dev |  | The population standard deviation for the data range and is assumed to be known. |
| Size |  | The sample size. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

## Remarks

The confidence interval is a range of values. Your sample mean, x, is at the center of this range and the range is x ± CONFIDENCE.NORM. For example, if x is the sample mean of delivery times for products ordered through the mail, x ± CONFIDENCE.NORM is a range of population means.

## Examples

```dax


--  CONFIDENCE.NORM returns the confidence interval

--  for a population mean, assuming a normal distribution

--  CONFIDENCE.T uses a Student's T distribution instead

--

--      CONFIDENCE.NORM ( Alpha, StandardDev, SampleSize )

--

--  Confidence level equals 100*(1-alpha)%

--  Alpha 0.05 indicates a 95% confidence level

DEFINE

    TABLE SampleData = { 2, 4, 4, 4, 5, 5, 7, 9 }

    VAR Mean         = AVERAGE ( SampleData[Value] )

    VAR StandardDev  = STDEV.S ( SampleData[Value] )

    VAR SampleSize   = COUNTROWS ( SampleData )

    VAR Alpha        = 0.05 -- 95% confidence level

EVALUATE

{

    ( "AVERAGE",          Mean ),

    ( "STDEV.S",          StandardDev ),

    ( "CONFIDENCE.NORM",  CONFIDENCE.NORM ( Alpha, StandardDev, SampleSize ) ),

    ( "CONFIDENCE.T",     CONFIDENCE.T    ( Alpha, StandardDev, SampleSize ) )

}



```

| Value1 | Value2 |
| --- | --- |
| AVERAGE | 5.00 |
| STDEV.S | 2.14 |
| CONFIDENCE.NORM | 1.48 |
| CONFIDENCE.T | 1.79 |

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/confidence-norm-function-dax](https://docs.microsoft.com/en-us/dax/confidence-norm-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
