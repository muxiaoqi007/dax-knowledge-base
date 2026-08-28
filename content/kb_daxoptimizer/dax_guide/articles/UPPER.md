---
title: "UPPER"
function: "upper"
category: "Text"
url: "https://dax.guide/upper/"
source: "dax.guide"
重要度:
难度:
---

# UPPER DAX Function (Text)

Converts a text string to all uppercase letters.

## Syntax

UPPER ( <Text> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Text |  | The text you want converted to uppercase, or a reference to a cell that contains a text string. |

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

Text in uppercase.

[» 1 related function](#alt)  

## Examples

```dax


--  UPPER and LOWER convert their only argument in uppercase or lowercase, respectively

DEFINE 

    VAR Val = "DAX is so cool!"

EVALUATE

{

    ( "Original = " &      Val   ),

    ( "UPPER = " & UPPER ( Val ) ),

    ( "LOWER = " & LOWER ( Val ) )

}

```

| Value |
| --- |
| Original = DAX is so cool! |
| UPPER = DAX IS SO COOL! |
| LOWER = dax is so cool! |

## Related functions

Other related functions are:

- [[LOWER]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/upper-function-dax](https://docs.microsoft.com/en-us/dax/upper-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
