---
title: "UNICODE"
function: "unicode"
category: "Text"
url: "https://dax.guide/unicode/"
source: "dax.guide"
重要度:
难度:
---

# UNICODE DAX Function (Text)

Returns the number (code point) corresponding to the first character of the text.

## Syntax

UNICODE ( <Text> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Text |  | Text is the character for which you want the Unicode value. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

A numeric code for the first character in a text string.

## Remarks

The Unicode encoding used by DAX is UTF-16.

[» 1 related article](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  UNICHAR returns a character given the unicode value.

--  UNICODE returns the Unicode value for a character

--      The example uses UNICODE ( "━" ) = 9473

EVALUATE 

{ 

    ( "UNICODE ( ""━"" )", UNICODE ( "━" ) ), 

    ("UNICHAR ( 9473 )",   UNICHAR ( 9473 ) ) 

}



EVALUATE

ADDCOLUMNS (

    VALUES ( 'Product'[Color] ),

    "Histogram", REPT ( UNICHAR ( 9473 ), [Sales Amount] / 1000000 )

)



```

| Value1 | Value2 |
| --- | --- |
| UNICODE ( “━” ) | 9473 |
| UNICHAR ( 9473 ) | ━ |

| Color | Histogram |
| --- | --- |
| Silver | ━━━━━━━ |
| Blue | ━━ |
| White | ━━━━━━ |
| Red | ━ |
| Black | ━━━━━━ |
| Green | ━ |
| Orange | ━ |
| Pink | ━ |
| Yellow |  |
| Purple |  |
| Brown | ━ |
| Grey | ━━━━ |
| Gold |  |
| Azure |  |
| Silver Grey |  |
| Transparent |  |

## Related articles

Learn more about UNICODE in the following articles:

- [**Improving data labels with format strings**](https://www.sqlbi.com/articles/improving-data-labels-with-format-strings/)

  This article describes the different approaches to format your DAX measures in Power BI semantic models using format custom and dynamic format strings. [» Read more](https://www.sqlbi.com/articles/improving-data-labels-with-format-strings/)

## Related functions

Other related functions are:

- [[UNICHAR]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/unicode-function-dax](https://docs.microsoft.com/en-us/dax/unicode-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
