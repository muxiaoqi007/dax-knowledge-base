---
title: "PERCENTILE.INC"
function: "percentile-inc"
category: "Statistical"
url: "https://dax.guide/percentile-inc/"
source: "dax.guide"
重要度:
难度:
---

# PERCENTILE.INC DAX Function (Statistical)

Returns the k-th (inclusive) percentile of values in a column.

## Syntax

PERCENTILE.INC ( <Column>, <K> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Column |  | A column containing the values. |
| K |  | Desired percentile value in the interval [0,1]. |

## Return values

Scalar A single [variant](https://dax.guide/dt/variant/) value.

Percentile value.

## Remarks

[PERCENTILEX.INC](https://dax.guide/percentilex.inc/) considers the blank values returned by Expression. This behavior is different from PERCENTILE.INC, which ignores the blank values.

[» 1 related article](#articles)  
[» 3 related functions](#alt)  

## Examples

```dax


--  PERCENTILE.EXC computed the k-th percentile, exclusive

--  PERCENTILE.INC computed the k-th percentile, inclusive

--

--  Both functions rank the N values from 1 (lowest)

--  to N (highest), then determine the possibly-non-integer 

--  calculated rank for the specified percentage argument K 

--  (a decimal number between 0.00 and 1.00), and finally 

--  use linear interpolation between the closest integer-rank 

--  values of the data array.

--

--  For PERCENTILE.EXC the calculated rank is K*(N+1)

--  For PERCENTILE.INC the calculated rank is K*(N-1)+1

--

--  MEDIAN corresponds to PERCENTILE.INC with k=0.50

DEFINE

    TABLE SampleData = { 2, 4, 4, 4, 5, 5, 7, 9 }

EVALUATE

{

     ( "AVERAGE",  AVERAGE ( SampleData[Value] ) ),

     ( "MEDIAN",   MEDIAN ( SampleData[Value] ) ),

     ( "PERCENTILE.EXC 0.25",  PERCENTILE.EXC ( SampleData[Value], 0.25 ) ),

     ( "PERCENTILE.INC 0.25",  PERCENTILE.INC ( SampleData[Value], 0.25 ) ),

     ( "PERCENTILE.EXC 0.50",  PERCENTILE.EXC ( SampleData[Value], 0.50 ) ),

     ( "PERCENTILE.INC 0.50",  PERCENTILE.INC ( SampleData[Value], 0.50 ) ),

     ( "PERCENTILE.EXC 0.75",  PERCENTILE.EXC ( SampleData[Value], 0.75 ) ),

     ( "PERCENTILE.INC 0.75",  PERCENTILE.INC ( SampleData[Value], 0.75 ) )

}



```

| Value1 | Value2 |
| --- | --- |
| AVERAGE | 5.00 |
| MEDIAN | 4.50 |
| PERCENTILE.EXC 0.25 | 4.00 |
| PERCENTILE.INC 0.25 | 4.00 |
| PERCENTILE.EXC 0.50 | 4.50 |
| PERCENTILE.INC 0.50 | 4.50 |
| PERCENTILE.EXC 0.75 | 6.50 |
| PERCENTILE.INC 0.75 | 5.50 |

```dax


--

-- Different handling of blanks between PERCENTILE and PERCENTILEX

-- PERCENTILE ignores blank values

-- PERCENTILEX considers blank values

--

DEFINE

    TABLE SampleDataWithBlanks =

        { BLANK (), BLANK (), BLANK (), 1, 2, 3, 4 }



EVALUATE

{

    ( "PERCENTILE.INC 0.25", PERCENTILE.INC ( SampleDataWithBlanks[Value], 0.25 ) ),

    ( "PERCENTILE.INC 0.50", PERCENTILE.INC ( SampleDataWithBlanks[Value], 0.50 ) ),

    ( "PERCENTILE.INC 0.75", PERCENTILE.INC ( SampleDataWithBlanks[Value], 0.75 ) ),

    ( "PERCENTILEX.INC 0.25", PERCENTILEX.INC ( SampleDataWithBlanks, SampleDataWithBlanks[Value], 0.25 ) ),

    ( "PERCENTILEX.INC 0.50", PERCENTILEX.INC ( SampleDataWithBlanks, SampleDataWithBlanks[Value], 0.50 ) ),

    ( "PERCENTILEX.INC 0.75", PERCENTILEX.INC ( SampleDataWithBlanks, SampleDataWithBlanks[Value], 0.75 ) )

}



```

| Value1 | Value2 |
| --- | --- |
| PERCENTILE.INC 0.25 | 1.75 |
| PERCENTILE.INC 0.50 | 2.50 |
| PERCENTILE.INC 0.75 | 3.25 |
| PERCENTILEX.INC 0.25 | (Blank) |
| PERCENTILEX.INC 0.50 | 1.00 |
| PERCENTILEX.INC 0.75 | 2.50 |

## Related articles

Learn more about PERCENTILE.INC in the following articles:

- [**Statistical Patterns**](https://www.daxpatterns.com/statistical-patterns/)

  DAX includes a few statistical aggregation functions, such as average, variance, and standard deviation. Other typical statistical calculations require you to write longer DAX expressions. Excel, from this point of view, has a much richer language. The Statistical Patterns are a collection of common statistical calculations: median, mode, moving average, percentile, and quartile. [» Read more](https://www.daxpatterns.com/statistical-patterns/)

## Related functions

Other related functions are:

- [[PERCENTILE.EXC]]
- [[PERCENTILEX.EXC]]
- [[PERCENTILEX.INC]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Ville-Pietari Louhiala, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/percentile-inc-function-dax](https://docs.microsoft.com/en-us/dax/percentile-inc-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
