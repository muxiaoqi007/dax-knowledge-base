---
title: "FIXED"
function: "fixed"
category: "Text"
url: "https://dax.guide/fixed/"
source: "dax.guide"
重要度:
难度:
---

# FIXED DAX Function (Text)

Rounds a number to the specified number of decimals and returns the result as text with optional commas.

## Syntax

FIXED ( <Number> [, <Decimals>] [, <NoCommas>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | The number you want to round and convert to text. |
| Decimals | Optional | The number of digits to the right of the decimal point; if omitted, 2. A negative number round to the left of the decimal point. |
| NoCommas | Optional | A logical value: if TRUE, do not display commas in the returned text; if FALSE or omitted, display commas in the returned text. |

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

A number represented as text.

## Remarks

If the value used for the **Decimals** parameter is negative, **Number** is rounded to the left of the decimal point.

If you omit **Decimals**, it is assumed to be 2.

If **NoCommas** is 0 or is omitted, then the returned text includes commas as usual.

[» 8 related functions](#alt)  

## Examples

```dax


--  FIXED returns a number as a string after having rounded to the

--  desired number of decimals, adding commas by default.

EVALUATE

ADDCOLUMNS (

    VALUES ( 'Product'[Category] ),

    "FIXED with commas",      FIXED ( [Sales Amount], 2, FALSE ),

    "FIXED without commas",   FIXED ( [Sales Amount], 2, TRUE ),

    "FIXED without decimals", FIXED ( [Sales Amount], 0, TRUE ),

    "FIXED rounded to 100",   FIXED ( [Sales Amount], -2, FALSE ),

    "Sales Amount",           [Sales Amount]

)

```

| Category | FIXED with commas | FIXED without commas | FIXED without decimals | FIXED rounded to 100 | Sales Amount |
| --- | --- | --- | --- | --- | --- |
| Audio | 384,518.16 | 384518.16 | 384518 | 384,500 | 384,518.16 |
| TV and Video | 4,392,768.29 | 4392768.29 | 4392768 | 4,392,800 | 4,392,768.29 |
| Computers | 6,741,548.73 | 6741548.73 | 6741549 | 6,741,500 | 6,741,548.73 |
| Cameras and camcorders | 7,192,581.95 | 7192581.95 | 7192582 | 7,192,600 | 7,192,581.95 |
| Cell phones | 1,604,610.26 | 1604610.26 | 1604610 | 1,604,600 | 1,604,610.26 |
| Music, Movies and Audio Books | 314,206.74 | 314206.74 | 314207 | 314,200 | 314,206.74 |
| Games and Toys | 360,652.81 | 360652.81 | 360653 | 360,700 | 360,652.81 |
| Home Appliances | 9,600,457.04 | 9600457.04 | 9600457 | 9,600,500 | 9,600,457.04 |

## Related functions

Other related functions are:

- [[CEILING]]
- [[FLOOR]]
- [[ISO.CEILING]]
- [[MROUND]]
- [[ROUND]]
- [[ROUNDDOWN]]
- [[ROUNDUP]]
- [[FORMAT]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/fixed-function-dax](https://docs.microsoft.com/en-us/dax/fixed-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
