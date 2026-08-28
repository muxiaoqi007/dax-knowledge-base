---
title: "MOD"
function: "mod"
category: "Math and Trig"
url: "https://dax.guide/mod/"
source: "dax.guide"
重要度:
难度:
---

# MOD DAX Function (Math and Trig)

Returns the remainder after a number is divided by a divisor.

## Syntax

MOD ( <Number>, <Divisor> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | The number for which you want to find the remainder after the division is performed. |
| Divisor |  | The number by which you want to divide. |

## Return values

Scalar A single value of any type.

The remainder value.

[» 1 related article](#articles)  
[» 3 related functions](#alt)  

## Examples

```dax


MOD ( 0, 2 ) -- returns 0

MOD ( 5, 2 ) -- returns 1

MOD ( 6, 3 ) -- returns 0

MOD ( 7, 3 ) -- returns 1

MOD ( 8, 3 ) -- returns 2

```

```dax


--  MOD returns the remainder of an integer division by A and B

--  whereas QUOTIENT returns the integer part of the quotient

DEFINE

    VAR Val1 = SELECTCOLUMNS ( GENERATESERIES ( 3, 6, 1 ), "Val1", [Value] )

    VAR Val2 = SELECTCOLUMNS ( GENERATESERIES ( 2, 4, 1 ), "Val2", [Value] )

EVALUATE

ADDCOLUMNS ( 

    CROSSJOIN ( Val1, Val2 ),

    "Division", DIVIDE   ( [Val1], [Val2] ),

    "QUOTIENT", QUOTIENT ( [Val1], [Val2] ),

    "MOD",      MOD      ( [Val1], [Val2] ),

    "Mod/Div",  MOD ( [Val1], [Val2] ) / [Val2]

)

ORDER BY [Val1] DESC, [Val2] ASC

```

| Val1 | Val2 | Division | Quotient | MOD | Mod/Div |
| --- | --- | --- | --- | --- | --- |
| 6 | 2 | 3.00 | 3 | 0 | 0.00 |
| 6 | 3 | 2.00 | 2 | 0 | 0.00 |
| 6 | 4 | 1.50 | 1 | 2 | 0.50 |
| 5 | 2 | 2.50 | 2 | 1 | 0.50 |
| 5 | 3 | 1.67 | 1 | 2 | 0.67 |
| 5 | 4 | 1.25 | 1 | 1 | 0.25 |
| 4 | 2 | 2.00 | 2 | 0 | 0.00 |
| 4 | 3 | 1.33 | 1 | 1 | 0.33 |
| 4 | 4 | 1.00 | 1 | 0 | 0.00 |
| 3 | 2 | 1.50 | 1 | 1 | 0.50 |
| 3 | 3 | 1.00 | 1 | 0 | 0.00 |
| 3 | 4 | 0.75 | 0 | 3 | 0.75 |

```dax


--  MOD returns the remainder of an integer division by A and B

--  whereas QUOTIENT returns the integer part of the quotient.

DEFINE

    VAR Val1 = SELECTCOLUMNS ( GENERATESERIES ( 0.5, 3.3, 1.6 ), "Val1", [Value] )

    VAR Val2 = SELECTCOLUMNS ( GENERATESERIES ( 0.5, 2.8, 1.1 ), "Val2", [Value] )

EVALUATE

ADDCOLUMNS ( 

    CROSSJOIN ( Val1, Val2 ),

    "Division", DIVIDE   ( [Val1], [Val2] ),

    "QUOTIENT", QUOTIENT ( [Val1], [Val2] ),

    "MOD",      MOD      ( [Val1], [Val2] ),

    "Mod/Div",  MOD ( [Val1], [Val2] ) / [Val2]

)

ORDER BY [Val1] DESC, [Val2] ASC

```

| Val1 | Val2 | Division | Quotient | MOD | Mod/Div |
| --- | --- | --- | --- | --- | --- |
| 2.10 | 0.50 | 4.20 | 4 | 0.10 | 0.20 |
| 2.10 | 1.60 | 1.31 | 1 | 0.50 | 0.31 |
| 2.10 | 2.70 | 0.78 | 0 | 2.10 | 0.78 |
| 0.50 | 0.50 | 1.00 | 1 | 0.00 | 0.00 |
| 0.50 | 1.60 | 0.31 | 0 | 0.50 | 0.31 |
| 0.50 | 2.70 | 0.19 | 0 | 0.50 | 0.19 |

## Related articles

Learn more about MOD in the following articles:

- [**Applying color banding by document number in Power BI**](https://www.sqlbi.com/articles/applying-color-banding-by-document-number-in-power-bi/)

  This article describes how to use conditional formatting with a DAX expression to color the rows of a table in Power BI based on the order number instead of using the alternate rows color formatting option. [» Read more](https://www.sqlbi.com/articles/applying-color-banding-by-document-number-in-power-bi/)

## Related functions

Other related functions are:

- [[DIVIDE]]
- [[LCM]]
- [[QUOTIENT]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/mod-function-dax](https://docs.microsoft.com/en-us/dax/mod-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
