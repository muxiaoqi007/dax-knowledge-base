---
title: "IF.EAGER"
function: "if-eager"
category: "Logical"
url: "https://dax.guide/if-eager/"
source: "dax.guide"
重要度:
难度:
---

# IF.EAGER DAX Function (Logical)

Checks whether a condition is met, and returns one value if TRUE, and another value if FALSE. Uses eager execution plan.

## Syntax

IF.EAGER ( <LogicalTest>, <ResultIfTrue> [, <ResultIfFalse>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| LogicalTest |  | Any value or expression that can be evaluated to TRUE or FALSE. |
| ResultIfTrue |  | The value that is returned if the logical test is TRUE. |
| ResultIfFalse | Optional | The value that is returned if the logical test is FALSE; if omitted, [[BLANK]] is returned. |

## Return values

Scalar A single value of any type.

Either ResultIfTrue or ResultIfFalse expression result, depending on LogicalTest.  
The result data type can be variant if ResultIfTrue and ResultIfFalse are of different data types. The function attempts to return a single data type if both ResultIfTrue and ResultIfFalse are of numeric data types. In the latter case, the IF.EAGER function will implicitly convert data types to accommodate both values.

## Remarks

IF.EAGER has the same functional behavior as the [[IF]] function, but performance may differ due to differences in execution plans. IF.EAGER has the same execution plan as the following DAX expression:

```dax


VAR _value_if_true = <ResultIfTrue>

VAR _value_if_false = <ResultIfFalse>

RETURN

    IF ( <LogicalTest>, _value_if_true, _value_if_false )

```

**Note**: The two branch expressions are evaluated regardless of the condition expression.

Calling IF.EAGER enforce eager evaluation of the conditional expression, instead of relying on choice between strict and eager evaluation made by the DAX engine.

This function should be used only in very particular case of DAX optimization, after verifying that it produces a clear performance advantage compared to the regular [[IF]] function.

[» 1 related article](#articles)  
[» 3 related functions](#alt)  

## Examples

```dax


--  The engine can use either STRICT or EAGER evaluation. 

--  The choice is up to the optimizer.

--  Using IF.EAGER you can force eager evaluation of IF statements.

--  Useful in some optimization scenarios.

DEFINE

    MEASURE Sales[GrossAmount] = SUMX ( Sales, Sales[Quantity] * Sales[Unit Price] )

    MEASURE Sales[NetAmount]   = SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

EVALUATE

ADDCOLUMNS (

    VALUES ( 'Product'[Brand] ),

    "Sales",

        IF ( 'Product'[Brand] = "Contoso", [GrossAmount], [NetAmount] * 1.5 )

)

ORDER BY 'Product'[Brand]

```

| Brand | Sales |
| --- | --- |
| A. Datum | 3,144,276.96 |
| Adventure Works | 6,016,668.41 |
| Contoso | 8,098,022.18 |
| Fabrikam | 8,331,023.59 |
| Litware | 4,883,556.04 |
| Northwind Traders | 1,560,828.19 |
| Proseware | 3,819,216.24 |
| Southridge Video | 2,076,620.77 |
| Tailspin Toys | 487,563.63 |
| The Phone Company | 1,685,728.60 |
| Wide World Importers | 2,852,934.98 |

## Related articles

Learn more about IF.EAGER in the following articles:

- [**Understanding eager vs. strict evaluation in DAX**](https://www.sqlbi.com/articles/understanding-eager-vs-strict-evaluation-in-dax/)

  This article describes the differences between eager evaluation and strict evaluation in DAX, empowering you to choose the best evaluation type for your data models. [» Read more](https://www.sqlbi.com/articles/understanding-eager-vs-strict-evaluation-in-dax/)

## Related functions

Other related functions are:

- [[COALESCE]]
- [[IF]]
- [[SWITCH]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/if-eager-function-dax](https://docs.microsoft.com/en-us/dax/if-eager-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
