---
title: "LEFT"
function: "left"
category: "Text"
url: "https://dax.guide/left/"
source: "dax.guide"
重要度:
难度:
---

# LEFT DAX Function (Text)

Returns the specified number of characters from the start of a text string.

## Syntax

LEFT ( <Text> [, <NumberOfCharacters>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Text |  | The text string containing the characters you want to extract. |
| NumberOfCharacters | Optional | The number of characters you want LEFT to extract; if omitted, 1. |

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

A text string.

[» 4 related articles](#articles)  
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

## Related articles

Learn more about LEFT in the following articles:

- [**From SQL to DAX: String Comparison**](https://www.sqlbi.com/articles/from-sql-to-dax-string-comparison/)

  In DAX string comparison requires you more attention than in SQL, for several reasons: DAX doesn’t offer the same set of features you have in SQL, a few text comparison functions in DAX are only case-sensitive and others only case-insensitive,… [» Read more](https://www.sqlbi.com/articles/from-sql-to-dax-string-comparison/)
- [**Using join functions in DAX**](https://www.sqlbi.com/articles/using-join-functions-in-dax/)

  This article describes the practical uses of NATURALLEFTOUTERJOIN and NATURALINNERJOIN in DAX. These functions are not commonly used in DAX because they do not have the same flexibility as the corresponding concepts in SQL. [» Read more](https://www.sqlbi.com/articles/using-join-functions-in-dax/)
- [**Finding products without sales by using DAX**](https://www.sqlbi.com/articles/finding-products-without-sales-by-using-dax/)

  This article analyzes the performance of different DAX techniques to identify any products without sales in an area or a time period. [» Read more](https://www.sqlbi.com/articles/finding-products-without-sales-by-using-dax/)
- [**Managing hierarchical organizations in Power BI security roles**](https://www.sqlbi.com/articles/managing-hierarchical-organizations-in-power-bi-security-roles/)

  This article describes how to apply dynamic security roles in a hierarchical organization to minimize the maintenance effort on the security configuration and obtain the best performance at query time. [» Read more](https://www.sqlbi.com/articles/managing-hierarchical-organizations-in-power-bi-security-roles/)

## Related functions

Other related functions are:

- [[MID]]
- [[RIGHT]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/left-function-dax](https://docs.microsoft.com/en-us/dax/left-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
