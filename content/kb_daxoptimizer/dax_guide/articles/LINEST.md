---
title: "LINEST"
function: "linest"
category: "Statistical"
url: "https://dax.guide/linest/"
source: "dax.guide"
重要度:
难度:
---

# LINEST DAX Function (Statistical)

Uses the Least Squares method to calculate a straight line that best fits the data, then returns a table describing the line. The equation for the line is of the form: y = m1\*x1 + m2\*x2 + … + b.

## Syntax

LINEST ( <ColumnY>, <ColumnX> [, <ColumnX> [, … ] ] [, <Const>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnY |  | The column of known y-values. |
| ColumnX | Repeatable | The columns of known x-values. |
| Const | Optional | A constant TRUE/FALSE value specifying whether to force the constant b to equal 0. If true or omitted, b is calculated normally. If false, b is set to 0. |

## Return values

Table An entire table or a table with one or more columns.

The table returned has the following columns:

- SlopeN (one for each X column reference)
- Intercept
- StandardErrorSlopeN (one for each X column reference)
- StandardErrorIntercept
- CoefficientOfDetermination
- StandardError
- FStatistic
- DegreesOfFreedom
- RegressionSumOfSquares
- ResidualSumOfSquares

## Remarks

All the column references must be from the same table.

[» 1 related article](#articles)  

## Examples

```dax


DEFINE

    TABLE Test =

        SELECTCOLUMNS (

            {

                ( 2310, 2, 2, 20, 142000 ),

                ( 2333, 2, 2, 12, 144000 ),

                ( 2356, 3, 1.5, 33, 151000 ),

                ( 2379, 3, 2, 43, 150000 ),

                ( 2402, 2, 3, 53, 139000 ),

                ( 2425, 4, 2, 23, 169000 ),

                ( 2448, 2, 1.5, 99, 126000 ),

                ( 2471, 2, 2, 34, 142900 ),

                ( 2494, 3, 3, 23, 163000 ),

                ( 2517, 4, 4, 55, 169000 ),

                ( 2540, 2, 3, 22, 149000 )

            },

            "Floor space (x1)", [Value1],

            "Offices (x2)", [Value2],

            "Entrances (x3)", [Value3],

            "Age (x4)", [Value4],

            "Assessed value (y)", [Value5]

        )



EVALUATE

Test



EVALUATE

LINEST (

    Test[Assessed value (y)],

    Test[Floor space (x1)],

    Test[Offices (x2)],

    Test[Entrances (x3)],

    Test[Age (x4)]

)

```

| Floor space (x1) | Offices (x2) | Entrances (x3) | Age (x4) | Assessed value (y) |
| --- | --- | --- | --- | --- |
| 2,310 | 2 | 2.00 | 20 | 142,000 |
| 2,333 | 2 | 2.00 | 12 | 144,000 |
| 2,356 | 3 | 1.50 | 33 | 151,000 |
| 2,379 | 3 | 2.00 | 43 | 150,000 |
| 2,402 | 2 | 3.00 | 53 | 139,000 |
| 2,425 | 4 | 2.00 | 23 | 169,000 |
| 2,448 | 2 | 1.50 | 99 | 126,000 |
| 2,471 | 2 | 2.00 | 34 | 142,900 |
| 2,494 | 3 | 3.00 | 23 | 163,000 |
| 2,517 | 4 | 4.00 | 55 | 169,000 |
| 2,540 | 2 | 3.00 | 22 | 149,000 |

| Slope1 | Slope2 | Slope3 | Slope4 | Intercept | StandardErrorSlope1 | StandardErrorSlope2 | StandardErrorSlope3 | StandardErrorSlope4 | StandardErrorIntercept | CoefficientOfDetermination | StandardError | FStatistic | DegreesOfFreedom | RegressionSumOfSquares | ResidualSumOfSquares |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 27.64 | 12,529.77 | 2,553.21 | -234.24 | 52,317.83 | 5.43 | 400.07 | 530.67 | 13.27 | 12,237.36 | 1.00 | 970.58 | 459.75 | 6 | 1,732,393,319.23 | 5,652,135.32 |

## Related articles

Learn more about LINEST in the following articles:

- [**Implementing linear regression in Power BI**](https://www.sqlbi.com/articles/implementing-linear-regression-in-power-bi/)

  The LINESTX DAX function simplifies the calculation of linear regressions. This article explains the correct way to manage its results efficiently. [» Read more](https://www.sqlbi.com/articles/implementing-linear-regression-in-power-bi/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/dax/linest-function-dax](https://learn.microsoft.com/dax/linest-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
