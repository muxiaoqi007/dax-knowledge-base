---
title: "STDEVX.P"
function: "stdevx-p"
category: "Statistical"
url: "https://dax.guide/stdevx-p/"
source: "dax.guide"
重要度:
难度:
---

# STDEVX.P DAX Function (Statistical)

Estimates standard deviation based on the entire population that results from evaluating an expression for each row of a table.

## Syntax

STDEVX.P ( <Table>, <Expression> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | The table containing the rows for which the expression will be evaluated. || Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | The expression to be evaluated for each row of the table. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

The standard deviation of the entire population.

## Remarks

STDEVX.P assumes that the column refers to the entire population.

To compute the standard deviation with a sample of the population, use [STDEVX.S](https://dax.guide/stdevx.s/).

[» 1 related article](#articles)  
[» 3 related functions](#alt)  

## Examples

```dax


--  Computes the standard deviation over a table of values

--

--  STDEV.P : standard deviation over the entire population

--  STDEV.S : standard deviation over a sample of the entire population

--

--  STDEVX is an iterator, STDEV is the simplified version in case

--  you are using a single column.

DEFINE

    TABLE SampleData = { 2, 4, 4, 4, 5, 5, 7, 9 }

EVALUATE

{

     ( "STDEV.P",  STDEV.P ( SampleData[Value] ) ),

     ( "STDEV.S",  STDEV.S ( SampleData[Value] ) ),

     ( "STDEVX.P", STDEVX.P ( SampleData, SampleData[Value] ) ),

     ( "STDEVX.S", STDEVX.S ( SampleData, SampleData[Value] ) ),

     ( "STDEVX.P", STDEVX.P ( Sales, Sales[Quantity] * Sales[Net Price] ) ),

     ( "STDEVX.S", STDEVX.S ( Sales, Sales[Quantity] * Sales[Net Price] ) )

}



-- The STDEV.S over SampleData is very different from STDEV.P because the 

-- set is small (8 rows for the population, 8 rows for the sample)

-- When applied to Sales, the difference is small in hidden decimals because

-- the set used has 100,000 rows



```

| Value1 | Value2 |
| --- | --- |
| STDEV.P | 2.0000000000 |
| STDEV.S | 2.1380899353 |
| STDEVX.P | 2.0000000000 |
| STDEVX.S | 2.1380899353 |
| STDEVX.P | 588.4956677430 |
| STDEVX.S | 588.4986034618 |

## Related articles

Learn more about STDEVX.P in the following articles:

- [**Statistical Patterns**](https://www.daxpatterns.com/statistical-patterns/)

  DAX includes a few statistical aggregation functions, such as average, variance, and standard deviation. Other typical statistical calculations require you to write longer DAX expressions. Excel, from this point of view, has a much richer language. The Statistical Patterns are a collection of common statistical calculations: median, mode, moving average, percentile, and quartile. [» Read more](https://www.daxpatterns.com/statistical-patterns/)

## Related functions

Other related functions are:

- [[STDEV.P]]
- [[STDEV.S]]
- [[STDEVX.S]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Tomasz Bobinski

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/stdevx-p-function-dax](https://docs.microsoft.com/en-us/dax/stdevx-p-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
