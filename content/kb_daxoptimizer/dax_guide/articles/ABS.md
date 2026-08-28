---
title: "ABS"
function: "abs"
category: "Math and Trig"
url: "https://dax.guide/abs/"
source: "dax.guide"
重要度:
难度:
---

# ABS DAX Function (Math and Trig)

Returns the absolute value of a number.

## Syntax

ABS ( <Number> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | The number for which you want the absolute value. |

## Return values

Scalar A single value of one these types: [currency](https://dax.guide/dt/currency), [decimal](https://dax.guide/dt/decimal), [integer](https://dax.guide/dt/integer).

## Remarks

The absolute value of a number with the same data type and without its sign.  
It also works with positive and negative infinite numbers.

[» 2 related articles](#articles)  

## Examples

```dax


--  ABS returns the absolute value of a number

--  SIGN returns:

--      +1 if the number is positive

--       0 if the number is zero

--      -1 if the number is negative

DEFINE

    VAR Vals = GENERATESERIES ( -2, +2, 0.5 )

EVALUATE

ADDCOLUMNS ( 

    Vals, 

    "ABS", ABS ( [Value] ), 

    "SIGN", SIGN ( [Value] )

)

ORDER BY [Value] DESC

```

| Value | ABS | SIGN |
| --- | --- | --- |
| 2.00 | 2.00 | 1 |
| 1.50 | 1.50 | 1 |
| 1.00 | 1.00 | 1 |
| 0.50 | 0.50 | 1 |
| 0.00 | 0.00 | 0 |
| -0.50 | 0.50 | -1 |
| -1.00 | 1.00 | -1 |
| -1.50 | 1.50 | -1 |
| -2.00 | 2.00 | -1 |

## Related articles

Learn more about ABS in the following articles:

- [**Using scatterplots to find details in reports**](https://www.sqlbi.com/articles/using-scatterplots-to-find-details-in-reports/)

  This article describes how you can use a scatterplot visual to make more effective Power BI reports. [» Read more](https://www.sqlbi.com/articles/using-scatterplots-to-find-details-in-reports/)
- [**Using EXPAND and COLLAPSE in visual calculations**](https://www.sqlbi.com/articles/using-expand-and-collapse-in-visual-calculations/)

  This article provides examples of visual calculations where the use of EXPAND and COLLAPSE is required to obtain the correct result. [» Read more](https://www.sqlbi.com/articles/using-expand-and-collapse-in-visual-calculations/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/abs-function-dax](https://docs.microsoft.com/en-us/dax/abs-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
