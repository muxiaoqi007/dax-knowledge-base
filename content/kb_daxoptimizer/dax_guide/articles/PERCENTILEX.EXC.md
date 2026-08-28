---
title: "PERCENTILEX.EXC"
function: "percentilex-exc"
category: "Statistical"
url: "https://dax.guide/percentilex-exc/"
source: "dax.guide"
重要度:
难度:
---

# PERCENTILEX.EXC DAX Function (Statistical)

Returns the k-th (exclusive) percentile of an expression values in a table.

## Syntax

PERCENTILEX.EXC ( <Table>, <Expression>, <K> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | Table over which the Expression will be evaluated. || Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | Expression to evaluate for each row of the table. |
| K |  | Desired percentile value in the interval [1/(n+1),1-1/(n+1)], where n is a number of valid data points. |

## Return values

Scalar A single [variant](https://dax.guide/dt/variant/) value.

Percentile value.

## Remarks

PERCENTILEX.EXC considers the blank values returned by Expression. This behavior is different from [PERCENTILE.EXC](https://dax.guide/percentile.exc/), which ignores the blank values.

[» 1 related article](#articles)  
[» 3 related functions](#alt)  

## Examples

```dax


--  PERCENTILEX iterates the table and compute the expression for each row, 

--  computing the k-th percentile 

EVALUATE

{

     ( "Average Sales",         AVERAGEX ( Sales, Sales[Quantity] * Sales[Net Price] ) ),

     ( "Median Sales",          MEDIANX ( Sales, Sales[Quantity] * Sales[Net Price] ) ),

     ( "Percentile Sales 0.25", PERCENTILEX.INC ( Sales, Sales[Quantity] * Sales[Net Price], 0.25 ) ),

     ( "Percentile Sales 0.50", PERCENTILEX.INC ( Sales, Sales[Quantity] * Sales[Net Price], 0.50 ) ),

     ( "Percentile Sales 0.75", PERCENTILEX.INC ( Sales, Sales[Quantity] * Sales[Net Price], 0.75 ) )

}



```

| Value1 | Value2 |
| --- | --- |
| Average Sales | 305.21 |
| Median Sales | 114.21 |
| Percentile Sales 0.25 | 22.99 |
| Percentile Sales 0.50 | 114.21 |
| Percentile Sales 0.75 | 345.60 |

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

    ( "PERCENTILE.EXC 0.25", PERCENTILE.EXC ( SampleDataWithBlanks[Value], 0.25 ) ),

    ( "PERCENTILE.EXC 0.50", PERCENTILE.EXC ( SampleDataWithBlanks[Value], 0.50 ) ),

    ( "PERCENTILE.EXC 0.75", PERCENTILE.EXC ( SampleDataWithBlanks[Value], 0.75 ) ),

    ( "PERCENTILEX.EXC 0.25", PERCENTILEX.EXC ( SampleDataWithBlanks, SampleDataWithBlanks[Value], 0.25 ) ),

    ( "PERCENTILEX.EXC 0.50", PERCENTILEX.EXC ( SampleDataWithBlanks, SampleDataWithBlanks[Value], 0.50 ) ),

    ( "PERCENTILEX.EXC 0.75", PERCENTILEX.EXC ( SampleDataWithBlanks, SampleDataWithBlanks[Value], 0.75 ) )

}



```

| Value1 | Value2 |
| --- | --- |
| PERCENTILE.EXC 0.25 | 1.25 |
| PERCENTILE.EXC 0.50 | 2.50 |
| PERCENTILE.EXC 0.75 | 3.75 |
| PERCENTILEX.EXC 0.25 | (Blank) |
| PERCENTILEX.EXC 0.50 | 1.00 |
| PERCENTILEX.EXC 0.75 | 3.00 |

## Related articles

Learn more about PERCENTILEX.EXC in the following articles:

- [**Statistical Patterns**](https://www.daxpatterns.com/statistical-patterns/)

  DAX includes a few statistical aggregation functions, such as average, variance, and standard deviation. Other typical statistical calculations require you to write longer DAX expressions. Excel, from this point of view, has a much richer language. The Statistical Patterns are a collection of common statistical calculations: median, mode, moving average, percentile, and quartile. [» Read more](https://www.daxpatterns.com/statistical-patterns/)

## Related functions

Other related functions are:

- [[PERCENTILE.EXC]]
- [[PERCENTILE.INC]]
- [[PERCENTILEX.INC]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Ville-Pietari Louhiala, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/percentilex-exc-function-dax](https://docs.microsoft.com/en-us/dax/percentilex-exc-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
