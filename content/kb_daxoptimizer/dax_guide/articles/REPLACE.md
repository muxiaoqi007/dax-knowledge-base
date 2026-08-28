---
title: "REPLACE"
function: "replace"
category: "Text"
url: "https://dax.guide/replace/"
source: "dax.guide"
重要度:
难度:
---

# REPLACE DAX Function (Text)

Replaces part of a text string with a different text string.

## Syntax

REPLACE ( <OldText>, <StartPosition>, <NumberOfCharacters>, <NewText> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| OldText |  | The string of text that contains the characters you want to replace. |
| StartPosition |  | The position of the character in old\_text that you want to replace with new\_text. |
| NumberOfCharacters |  | The number of characters that you want to replace. |
| NewText |  | The replacement text. |

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

The resulting string after applying the replacements.

## Remarks

The StartPosition starts from 1 for the first character in the string.

[» 1 related function](#alt)  

## Examples

```dax


--  REPLACE lets you replace part of a string with a new string

DEFINE 

    VAR Val = "DAX is so cool !"

    VAR Replacement = "fantastic"

EVALUATE

{

    ( "Original", Val ),

    ( "Replaced", REPLACE ( Val, 11, 4, Replacement ) )

}

```

| Value1 | Value2 |
| --- | --- |
| Original | DAX is so cool ! |
| Replaced | DAX is so fantastic ! |

## Related functions

Other related functions are:

- [[SUBSTITUTE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/replace-function-dax](https://docs.microsoft.com/en-us/dax/replace-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
