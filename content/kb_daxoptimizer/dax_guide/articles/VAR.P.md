---
title: "VAR.P"
function: "var-p"
category: "Statistical"
url: "https://dax.guide/var-p/"
source: "dax.guide"
重要度:
难度:
---

# VAR.P DAX Function (Statistical)

Calculates variance based on the entire population. Ignores logical values and text in the population.

## Syntax

VAR.P ( <ColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName |  | A column that contains values corresponding to a population. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

A number with the variance of the entire population.

## Remarks

VAR.P assumes that the column refers to the entire population.

To compute the variance with a sample of the population, use [VAR.S](https://dax.guide/var.s/).

The VAR.P function internally executes [VARX.P](https://dax.guide/varx.p/), without any performance difference.  
The following VAR.P call:

```dax


VAR.P ( table[column] )

```

corresponds to the following [VARX.P](https://dax.guide/varx.p/) call:

```dax


VARX.P (

    table,

    table[column]

)

```

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

Learn more about VAR.P in the following articles:

- [**Statistical Patterns**](https://www.daxpatterns.com/statistical-patterns/)

  IMPORTANT If you use Power BI, Analysis Services, or Excel 2016 or later versions, you can use the statistical functions in DAX. If you use Excel 2010 or Excel 2013, most of the DAX statistical functions are not available and you can rely on an alternative implementation based on DAX code as described in this page. DAX includes a few statistical aggregation functions, such as average, variance, and standard deviation. Other typical statistical calculations require you to write longer DAX expressions. Excel, from this point of view, has a much richer language. The Statistical Patterns are a collection of common […] [» Read more](https://www.daxpatterns.com/statistical-patterns/)

## Related functions

Other related functions are:

- [[VAR.S]]
- [[VARX.P]]
- [[VARX.S]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/var-p-function-dax](https://docs.microsoft.com/en-us/dax/var-p-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
