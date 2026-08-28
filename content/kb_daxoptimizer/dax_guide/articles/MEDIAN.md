---
title: "MEDIAN"
function: "median"
category: "Statistical"
url: "https://dax.guide/median/"
source: "dax.guide"
重要度:
难度:
---

# MEDIAN DAX Function (Statistical)

Returns the 50th percentile of values in a column.

## Syntax

MEDIAN ( <Column> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Column |  | A column containing the values. |

## Return values

Scalar A single [variant](https://dax.guide/dt/variant/) value.

Median value

## Remarks

Blanks are ignored. Only numeric data types are supported. Logical values, dates, and text columns are not supported.

The following MEDIAN call:

```dax


MEDIAN ( table[column] )

```

corresponds to the following [[MEDIANX]] call:

```dax


MEDIANX (

    table,

    table[column] 

)

```

The result is blank in case there are no rows in the table with a non-blank value.

[» 1 related article](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  MEDIAN is the compact version of MEDIANX

--  MEDIANX returns the 50th percentile of an expression

--          evaluated row-by-row on a table.

--  MEDIAN corresponds to PERCENTILE.INC with k=0.50

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

## Related articles

Learn more about MEDIAN in the following articles:

- [**Statistical Patterns**](https://www.daxpatterns.com/statistical-patterns/)

  IMPORTANT If you use Power BI, Analysis Services, or Excel 2016 or later versions, you can use the statistical functions in DAX. If you use Excel 2010 or Excel 2013, most of the DAX statistical functions are not available and you can rely on an alternative implementation based on DAX code as described in this page. DAX includes a few statistical aggregation functions, such as average, variance, and standard deviation. Other typical statistical calculations require you to write longer DAX expressions. Excel, from this point of view, has a much richer language. The Statistical Patterns are a collection of common […] [» Read more](https://www.daxpatterns.com/statistical-patterns/)

## Related functions

Other related functions are:

- [[MEDIANX]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Jes Hansen, Antti Komonen

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/median-function-dax](https://docs.microsoft.com/en-us/dax/median-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
