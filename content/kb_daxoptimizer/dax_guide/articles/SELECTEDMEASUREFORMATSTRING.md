---
title: "SELECTEDMEASUREFORMATSTRING"
function: "selectedmeasureformatstring"
category: "Information"
url: "https://dax.guide/selectedmeasureformatstring/"
source: "dax.guide"
重要度:
难度:
---

# SELECTEDMEASUREFORMATSTRING DAX Function (Information)

Returns format string for the measure that is currently being evaluated.

## Syntax

SELECTEDMEASUREFORMATSTRING ( )

This expression has no parameters.

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

Format string of the measure currently being evaluated – which is the measure identified by [[SELECTEDMEASURE]].

[» 6 related articles](#articles)  

## Examples

```dax


-- Format string expression for Thousands calculation item

-- in Scale calculation group, which is used in the following example:

VAR CurrentFormatString =

    SELECTEDMEASUREFORMATSTRING ()

VAR FormatStringContainsDot =

    SEARCH ( ".", CurrentFormatString, 1, 0 ) > 0

VAR Result =

    CurrentFormatString & IF ( NOT FormatStringContainsDot, ".00" ) & " K"

RETURN

    Result

```

```dax


--  SELECTEDMEASUREFORMATSTRING returns the format string of the 

--  selected measure. It can be used only in calculation items

--

--  Its main usage is to dynamically create a format string based on

--  the original format string, modified by the calculation item

EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Calendar Year],

    'Scale'[Scale],

    TREATAS ( { "CY 2009" }, 'Date'[Calendar Year] ),

    "Amount", [Sales Amount],

    "Format String", IGNORE('Sales'[_Sales Amount FormatString] )

)

ORDER BY

    'Date'[Calendar Year]

```

| Calendar Year | Scale | Amount | Format String |
| --- | --- | --- | --- |
| 2009-01-01 | Value | 9,353,814.87 | #,0.00 |
| 2009-01-01 | Thousands | 9,353.81 | #,0.00 K |
| 2009-01-01 | Millions | 9.35 | #,0.00 M |

## Related articles

Learn more about SELECTEDMEASUREFORMATSTRING in the following articles:

- [**Understanding Calculation Groups**](https://www.sqlbi.com/articles/understanding-calculation-groups/)

  This article explores the properties of calculation groups in detail and then it describes how a calculation item is applied to a measure. Before starting, we suggest you read the previous article that introduces calculation groups. [» Read more](https://www.sqlbi.com/articles/understanding-calculation-groups/)
- [**Currency conversion in Power BI reports**](https://www.sqlbi.com/articles/currency-conversion-in-power-bi-reports/)

  This article describes how to implement currency conversion for reporting purposes in Power BI. [» Read more](https://www.sqlbi.com/articles/currency-conversion-in-power-bi-reports/)
- [**Controlling Format Strings in Calculation Groups**](https://www.sqlbi.com/articles/controlling-format-strings-in-calculation-groups/)

  This article describes how to control format strings in calculation groups. Before starting, we suggest you read the previous articles in this series. [» Read more](https://www.sqlbi.com/articles/controlling-format-strings-in-calculation-groups/)
- [**Scripting syntax for calculation groups**](https://www.sqlbi.com/articles/scripting-syntax-for-calculation-groups/)

  This article introduces the syntax to describe in a textual form the DAX expressions and additional properties of calculation groups. [» Read more](https://www.sqlbi.com/articles/scripting-syntax-for-calculation-groups/)
- [**Dynamic format strings with calculation groups**](https://www.sqlbi.com/articles/dynamic-format-strings-with-calculation-groups/)

  This article shows two techniques based on calculation groups: how to implement dynamic format strings in regular measures, and how to perform weight conversion on the fly. [» Read more](https://www.sqlbi.com/articles/dynamic-format-strings-with-calculation-groups/)
- [**Controlling empty or multiple selections in calculation groups**](https://www.sqlbi.com/articles/controlling-empty-or-multiple-selections-in-calculation-groups/)

  This article describes how to control the execution of DAX code when there are either multiple or empty selections of calculation items in calculation groups. [» Read more](https://www.sqlbi.com/articles/controlling-empty-or-multiple-selections-in-calculation-groups/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/selectedmeasureformatstring-function-dax](https://docs.microsoft.com/en-us/dax/selectedmeasureformatstring-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
