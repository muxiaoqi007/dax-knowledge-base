---
title: "LOWER"
function: "lower"
category: "Text"
url: "https://dax.guide/lower/"
source: "dax.guide"
重要度:
难度:
---

# LOWER DAX Function (Text)

Converts all letters in a text string to lowercase.

## Syntax

LOWER ( <Text> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Text |  | The text you want to convert to lowercase. Characters that are not letters are not changed. |

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

Text in lowercase.

[» 1 related article](#articles)  
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

## Related articles

Learn more about LOWER in the following articles:

- [**Letter case-sensitivity in DAX, Power BI and Analysis Services**](https://www.sqlbi.com/articles/letter-case-sensitivity-in-dax-power-bi-and-analysis-services/)

  Power BI and Analysis Services are case-insensitive by default.Lowercase letters are identical to uppercase letters.This is mostly a good choice, but it also comes with unexpected consequences. In this article, we run through a set of queries to understand what to be aware of when working with a mixture of lowercase and uppercase strings in DAX. [» Read more](https://www.sqlbi.com/articles/letter-case-sensitivity-in-dax-power-bi-and-analysis-services/)

## Related functions

Other related functions are:

- [[UPPER]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/lower-function-dax](https://docs.microsoft.com/en-us/dax/lower-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
