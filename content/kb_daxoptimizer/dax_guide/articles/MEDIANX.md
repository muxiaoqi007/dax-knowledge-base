---
title: "MEDIANX"
function: "medianx"
category: "Statistical"
url: "https://dax.guide/medianx/"
source: "dax.guide"
重要度:
难度:
---

# MEDIANX DAX Function (Statistical)

Returns the 50th percentile of an expression values in a table.

## Syntax

MEDIANX ( <Table>, <Expression> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | Table over which the Expression will be evaluated. || Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | Expression to evaluate for each row of the table. |

## Return values

Scalar A single [variant](https://dax.guide/dt/variant/) value.

Median value

## Remarks

The behavior of MEDIANX and [[MEDIAN]] is different if the expressions return blanks, logical values, or text.

Expressions returning blanks are relevant to MEDIANX, whereas they are ignored by [[MEDIAN]].

The result is blank in case there are no rows in the table returning a non-blank value.

When Expression returns an Integer, the resulting data type of MEDIANX is Variant (Integer for non-ties, Decimal for ties). Assigning a variant to a calculated column is not possible, so a type conversion is required in that case by using [[CONVERT]].

[» 1 related article](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  MEDIAN is the compact version of MEDIANX

--  MEDIANX returns the 50th percentile of an expression

--  evaluated row-by-row on a table.

DEFINE

    TABLE SampleData = { 2, 4, 4, 4, 5, 5, 7, 9 }

EVALUATE

{

     ( "AVERAGE",  AVERAGE ( SampleData[Value] ) ),

     ( "MEDIAN",   MEDIAN ( SampleData[Value] ) ),

     ( "MEDIANX",  MEDIANX ( SampleData, SampleData[Value] ) ),

     ( "Average Sales", AVERAGEX ( Sales, Sales[Quantity] * Sales[Net Price] ) ),

     ( "Median Sales", MEDIANX ( Sales, Sales[Quantity] * Sales[Net Price] ) )

}

```

| Value1 | Value2 |
| --- | --- |
| AVERAGE | 5 |
| MEDIAN | 4.5 |
| MEDIANX | 4.5 |
| Average Sales | 305.2084083507091 |
| Median Sales | 114.21 |

```dax


--  MEDIAN differs from MEDIANX when there are BLANK values involved

DEFINE

    TABLE SampleData = { BLANK(), 2, 4, 4, 4, 5, 5, 7, 9 }

EVALUATE

{

     ( "AVERAGE",  AVERAGE ( SampleData[Value] ) ),

     ( "MEDIAN",   MEDIAN ( SampleData[Value] ) ),

     ( "MEDIANX",  MEDIANX ( SampleData, SampleData[Value] ) )

}

```

| Value1 | Value2 |
| --- | --- |
| AVERAGE | 5 |
| MEDIAN | 4.5 |
| MEDIANX | 4 |

```dax


--

-- Different handling of blanks between PERCENTILE and PERCENTILEX

-- PERCENTILE ignores blank values

-- PERCENTILEX considers blank values

--

-- MEDIANX corresponds to PERCENTILEX.INC with k=0.50

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

    ( "PERCENTILEX.INC 0.75", PERCENTILEX.INC ( SampleDataWithBlanks, SampleDataWithBlanks[Value], 0.75 ) ),

    ( "MEDIANX", MEDIANX ( SampleDataWithBlanks, SampleDataWithBlanks[Value] ) )

}



```

| Value1 | Value2 |
| --- | --- |
| PERCENTILE.INC 0.25 | 1.75 |
| PERCENTILE.INC 0.50 | 2.5 |
| PERCENTILE.INC 0.75 | 3.25 |
| PERCENTILEX.INC 0.25 | (Blank) |
| PERCENTILEX.INC 0.50 | 1 |
| PERCENTILEX.INC 0.75 | 2.5 |
| MEDIANX | 1 |

## Related articles

Learn more about MEDIANX in the following articles:

- [**Statistical Patterns**](https://www.daxpatterns.com/statistical-patterns/)

  IMPORTANT If you use Power BI, Analysis Services, or Excel 2016 or later versions, you can use the statistical functions in DAX. If you use Excel 2010 or Excel 2013, most of the DAX statistical functions are not available and you can rely on an alternative implementation based on DAX code as described in this page. DAX includes a few statistical aggregation functions, such as average, variance, and standard deviation. Other typical statistical calculations require you to write longer DAX expressions. Excel, from this point of view, has a much richer language. The Statistical Patterns are a collection of common […] [» Read more](https://www.daxpatterns.com/statistical-patterns/)

## Related functions

Other related functions are:

- [[MEDIAN]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Anders Bergmål

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/medianx-function-dax](https://docs.microsoft.com/en-us/dax/medianx-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
