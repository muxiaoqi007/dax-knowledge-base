---
title: "LEN"
function: "len"
category: "Text"
url: "https://dax.guide/len/"
source: "dax.guide"
重要度:
难度:
---

# LEN DAX Function (Text)

Returns the number of characters in a text string.

## Syntax

LEN ( <Text> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Text |  | The text whose length you want to find. Spaces count as characters. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

The number of characters in the text string.

[» 1 related article](#articles)  

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

## Related articles

Learn more about LEN in the following articles:

- [**Managing hierarchical organizations in Power BI security roles**](https://www.sqlbi.com/articles/managing-hierarchical-organizations-in-power-bi-security-roles/)

  This article describes how to apply dynamic security roles in a hierarchical organization to minimize the maintenance effort on the security configuration and obtain the best performance at query time. [» Read more](https://www.sqlbi.com/articles/managing-hierarchical-organizations-in-power-bi-security-roles/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/len-function-dax](https://docs.microsoft.com/en-us/dax/len-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
