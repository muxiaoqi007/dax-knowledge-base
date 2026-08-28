---
title: "CONCATENATE"
function: "concatenate"
category: "Text"
url: "https://dax.guide/concatenate/"
source: "dax.guide"
重要度:
难度:
---

# CONCATENATE DAX Function (Text)

Joins two text strings into one text string.

## Syntax

CONCATENATE ( <Text1>, <Text2> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Text1 |  | The first text string to be joined into a single text string. Strings can include text or numbers. |
| Text2 |  | The second text string to be joined into a single text string. Strings can include text or numbers. |

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

The concatenated string.

## Remarks

If you need to concatenate multiple columns, you can create a series of calculations or, better, use the [concatenation operator (&)](https://dax.guide/op/concatenation/) to join all of them in a simpler expression.

[» 1 related article](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  CONCATENATE concatenates two strings.

--  It is an alternative to the more commonly used & operator

--  that also allows to concatenate multiple expressions.

EVALUATE

ADDCOLUMNS (

    SUMMARIZE ( 

        TOPN ( 5, Customer ), 

        Customer[Customer Code], 

        Customer[Customer Name] 

    ),

    "Name and Code 1", 

        CONCATENATE ( 

            Customer[Customer Name], 

            Customer[Customer Code] 

        ),

    "Name and Code 2", 

        Customer[Customer Name] & Customer[Customer Code]

)

```

| Customer Code | Customer Name | Name and Code 1 | Name and Code 2 |
| --- | --- | --- | --- |
| 11024 | Xie, Russell | Xie, Russell11024 | Xie, Russell11024 |
| 11036 | Russell, Jennifer | Russell, Jennifer11036 | Russell, Jennifer11036 |
| 11041 | Carter, Amanda | Carter, Amanda11041 | Carter, Amanda11041 |
| 11043 | Simmons, Nathan | Simmons, Nathan11043 | Simmons, Nathan11043 |
| 11928 | Morris, Isabella | Morris, Isabella11928 | Morris, Isabella11928 |

## Related articles

Learn more about CONCATENATE in the following articles:

- [**Using CONCATENATEX in measures**](https://www.sqlbi.com/articles/using-concatenatex-in-measures/)

  This article showcases the use of CONCATENATEX, a handy DAX function to return a list of values in a measure. [» Read more](https://www.sqlbi.com/articles/using-concatenatex-in-measures/)

## Related functions

Other related functions are:

- [[CONCATENATEX]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/concatenate-function-dax](https://docs.microsoft.com/en-us/dax/concatenate-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
