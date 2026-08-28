---
title: "UNICHAR"
function: "unichar"
category: "Text"
url: "https://dax.guide/unichar/"
source: "dax.guide"
重要度:
难度:
---

# UNICHAR DAX Function (Text)

Returns the Unicode character that is referenced by the given numeric value.

## Syntax

UNICHAR ( <Number> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | Number is the Unicode number(code point) for which you want the Unicode character. |

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

A character represented by the Unicode number.

## Remarks

If numbers are numeric values that fall outside the allowable range (from 0 to 1,114,109), UNICHAR returns an error.

If the number is zero (0), UNICHAR returns an error.

The Unicode encoding used by DAX is UTF-16.

[» 5 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  UNICHAR returns a character given the Unicode value

--  UNICODE returns the Unicode value for a character

--      The example uses UNICODE ( "━" ) = 9473

EVALUATE 

{ 

    ( "UNICODE ( ""━"" )", UNICODE ( "━" ) ), 

    ( "UNICHAR ( 9473 )",  UNICHAR ( 9473 ) ) 

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

Learn more about UNICHAR in the following articles:

- [**Handling customers with the same name in Power BI**](https://www.sqlbi.com/articles/handling-customers-with-the-same-name-in-power-bi/)

  This article explains how to show different customers with the same name in a Power BI report by using zero-width spaces, thus simplifying the presentation without adding visible characters to make the names unique. [» Read more](https://www.sqlbi.com/articles/handling-customers-with-the-same-name-in-power-bi/)
- [**Sorting duplicated names in a level of a hierarchy with DAX**](https://www.sqlbi.com/articles/sorting-duplicated-names-in-a-level-of-a-hierarchy-with-dax/)

  This article describes how to use DAX calculated columns to sort names that look like duplicates at a certain level of a hierarchy, but are unique when considering their full path within the hierarchy. [» Read more](https://www.sqlbi.com/articles/sorting-duplicated-names-in-a-level-of-a-hierarchy-with-dax/)
- [**Displaying filter context in Power BI Tooltips**](https://www.sqlbi.com/articles/displaying-filter-context-in-power-bi-tooltips/)

  This article describes how to display the filter context applied to a calculation using a special DAX measure in Power BI Tooltips. [» Read more](https://www.sqlbi.com/articles/displaying-filter-context-in-power-bi-tooltips/)
- [**Improving data labels with format strings**](https://www.sqlbi.com/articles/improving-data-labels-with-format-strings/)

  This article describes the different approaches to format your DAX measures in Power BI semantic models using format custom and dynamic format strings. [» Read more](https://www.sqlbi.com/articles/improving-data-labels-with-format-strings/)
- [**Show transaction details on the matrix visual in Power BI**](https://www.sqlbi.com/articles/show-transaction-details-on-the-matrix-visual-in-power-bi/)

  This article shows how to create a DAX measure that displays information from multiple columns in a business entity or transaction, into a single column of a matrix. [» Read more](https://www.sqlbi.com/articles/show-transaction-details-on-the-matrix-visual-in-power-bi/)

## Related functions

Other related functions are:

- [[UNICODE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/unichar-function-dax](https://docs.microsoft.com/en-us/dax/unichar-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
