---
title: "VARX.S"
function: "varx-s"
category: "Statistical"
url: "https://dax.guide/varx-s/"
source: "dax.guide"
重要度:
难度:
---

# VARX.S DAX Function (Statistical)

Estimates variance based on a sample that results from evaluating an expression for each row of a table.

## Syntax

VARX.S ( <Table>, <Expression> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | The table containing the rows for which the expression will be evaluated. || Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | The expression to be evaluated for each row of the table. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

A number that represents the variance of a sample population.

## Remarks

VARX.S assumes that the column refers to a sample of the population.

To compute the variance with the entire population, use [VARX.P](https://dax.guide/varx.p/).

[» 1 related article](#articles)  
[» 3 related functions](#alt)  

## Examples

```dax


--  Computes the variance over a table of values

--

--  VAR.P : variance over the entire population

--  VAR.S : variance over a sample of the entire population

--

--  VARX is an iterator, VAR is the simplified version in case

--  you are using a single column

DEFINE

    TABLE SampleData = { 2, 4, 4, 4, 5, 5, 7, 9 }

EVALUATE

{

     ( "VAR.P",  VAR.P ( SampleData[Value] ) ),

     ( "VAR.S",  VAR.S ( SampleData[Value] ) ),

     ( "VARX.P", VARX.P ( SampleData, SampleData[Value] ) ),

     ( "VARX.S", VARX.S ( SampleData, SampleData[Value] ) ),

     ( "VARX.P", VARX.P ( Sales, Sales[Quantity] * Sales[Net Price] ) ),

     ( "VARX.S", VARX.S ( Sales, Sales[Quantity] * Sales[Net Price] ) )

}



-- The STDEV.S over SampleData is very different from STDEV.P because the 

-- set is small (8 rows for the population, 8 rows for the sample)

-- When applied to Sales, the difference is smaller because

-- the set used has 100,000 rows



```

| Value1 | Value2 |
| --- | --- |
| VAR.P | 4.00 |
| VAR.S | 4.57 |
| VARX.P | 4.00 |
| VARX.S | 4.57 |
| VARX.P | 346,327.15 |
| VARX.S | 346,330.61 |

## Related articles

Learn more about VARX.S in the following articles:

- [**Statistical Patterns**](https://www.daxpatterns.com/statistical-patterns/)

  IMPORTANT If you use Power BI, Analysis Services, or Excel 2016 or later versions, you can use the statistical functions in DAX. If you use Excel 2010 or Excel 2013, most of the DAX statistical functions are not available and you can rely on an alternative implementation based on DAX code as described in this page. DAX includes a few statistical aggregation functions, such as average, variance, and standard deviation. Other typical statistical calculations require you to write longer DAX expressions. Excel, from this point of view, has a much richer language. The Statistical Patterns are a collection of common […] [» Read more](https://www.daxpatterns.com/statistical-patterns/)

## Related functions

Other related functions are:

- [[VAR.P]]
- [[VAR.S]]
- [[VARX.P]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Daniel Otykier

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/varx-s-function-dax](https://docs.microsoft.com/en-us/dax/varx-s-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
