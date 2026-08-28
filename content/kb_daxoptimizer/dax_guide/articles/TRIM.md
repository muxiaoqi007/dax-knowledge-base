---
title: "TRIM"
function: "trim"
category: "Text"
url: "https://dax.guide/trim/"
source: "dax.guide"
重要度:
难度:
---

# TRIM DAX Function (Text)

Removes all spaces from a text string except for single spaces between words.

## Syntax

TRIM ( <Text> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Text |  | The text from which you want spaces removed. |

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

The string with spaces removed.

## Remarks

Use TRIM on text that you have received from another application that may have irregular spacing.

The TRIM function was originally designed to trim the 7-bit ASCII space character (value 32) from text. In the Unicode character set, there is an additional space character called the nonbreaking space character that has a decimal value of 160. This character is commonly used in Web pages as the HTML entity, &nbsp;. By itself, the TRIM function does not remove this nonbreaking space character.

[» 1 related function](#alt)  

## Examples

```dax


--  TRIM removes trailing and starting spaces from a string,

--  including multiple spaces within the string.

DEFINE 

    VAR Test = "  DAX is          awesome !!!    "

EVALUATE

    {

        ( "Original", Test ),

        ( "Trimmed",  TRIM ( Test ) )

    }

```

| Value1 | Value2 |
| --- | --- |
| Original | DAX is          awesome !!! |
| Trimmed | DAX is awesome !!! |

## Related functions

Other related functions are:

- [[REPLACE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/trim-function-dax](https://docs.microsoft.com/en-us/dax/trim-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
