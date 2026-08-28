---
title: "MID"
function: "mid"
category: "Text"
url: "https://dax.guide/mid/"
source: "dax.guide"
重要度:
难度:
---

# MID DAX Function (Text)

Returns a string of characters from the middle of a text string, given a starting position and length.

## Syntax

MID ( <Text>, <StartPosition>, <NumberOfCharacters> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Text |  | The text string from which you want to extract the characters. |
| StartPosition |  | The position of the first character you want to extract. Positions start at 1. |
| NumberOfCharacters |  | The number of characters to return. |

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

A string of text of the specified length.

[» 2 related functions](#alt)  

## Examples

```dax


--  LEFT, RIGHT, MID and LEN are the basic string-manipulation functions

DEFINE 

    VAR Val = "DAX is so cool!"

EVALUATE

{

    ( "LEFT ( Val, 3 ) ",   LEFT  ( Val, 3     ) ),

    ( "RIGHT ( Val, 5 )",   RIGHT ( Val, 5     ) ),

    ( "MID ( Val, 11, 4 )", MID   ( Val, 11, 4 ) ),

    ( "LEN ( Val )",        LEN   ( Val        ) )

}

```

| Value1 | Value2 |
| --- | --- |
| LEFT ( Val, 3 ) | DAX |
| RIGHT ( Val, 5 ) | cool! |
| MID ( Val, 11, 4 ) | cool |
| LEN ( Val ) | 15 |

```dax


EVALUATE

VAR SSN = "123-45-6789"

VAR SecureSSN = "XXX-XX-" & MID ( SSN, 8, 3 ) & "X"

RETURN { SecureSSN }

```

| Value |
| --- |
| XXX-XX-678X |

## Related functions

Other related functions are:

- [[LEFT]]
- [[RIGHT]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/mid-function-dax](https://docs.microsoft.com/en-us/dax/mid-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
