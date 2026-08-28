---
title: "GEOMEANX"
function: "geomeanx"
category: "Statistical"
url: "https://dax.guide/geomeanx/"
source: "dax.guide"
重要度:
难度:
---

# GEOMEANX DAX Function (Statistical)

Returns geometric mean of an expression values in a table.

## Syntax

GEOMEANX ( <Table>, <Expression> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | Table over which the Expression will be evaluated. || Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | Expression to evaluate for each row of the table. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Return a decimal number with the geometric mean.

[» 1 related function](#alt)  

## Examples

```dax


--  GEOMEANX is an iterator that computes the geometric mean of an expression

EVALUATE

{

     ( "Average",        AVERAGEX ( Sales, Sales[Quantity] * Sales[Net Price] ) ),

     ( "Geometric Mean", GEOMEANX ( Sales, Sales[Quantity] * Sales[Net Price] ) )

}





```

| Value1 | Value2 |
| --- | --- |
| Average | 305.21 |
| Geometric Mean | 89.14 |

## Related functions

Other related functions are:

- [[GEOMEAN]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Daniel Otykier

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/geomeanx-function-dax](https://docs.microsoft.com/en-us/dax/geomeanx-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
