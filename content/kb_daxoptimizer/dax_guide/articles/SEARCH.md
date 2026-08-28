---
title: "SEARCH"
function: "search"
category: "Text"
url: "https://dax.guide/search/"
source: "dax.guide"
重要度:
难度:
---

# SEARCH DAX Function (Text)

Returns the starting position of one text string within another text string. SEARCH is not case-sensitive, but it is accent-sensitive.

## Syntax

SEARCH ( <FindText>, <WithinText> [, <StartPosition>] [, <NotFoundValue>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| FindText |  | The text you want to find. You can use the ? and \* wildcard characters; use ~? and ~\* to find the ? and \* characters. |
| WithinText |  | The text in which you want to search for FindText. |
| StartPosition | Optional | The character position in WithinText at which you want to start searching. If omitted, the default value is 1. |
| NotFoundValue | Optional | The numeric value to be returned if the text is not found; if omitted, an error is returned. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

The number of the starting position of the first text string from the first character of the second text string.

## Remarks

SEARCH supports wildcards, whereas [[FIND]] does not.  
In order to find ~, you must use ~~.

[» 4 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  SEARCH searches the position of a substring inside a string.

--  Comparison is NOT case-sensitive.

--  You can provide the position from where to start searching

--  and a default value to return in case of no-match.

--  If no default value is provided, SEARCH raises an error in case 

--  the substring is not found.

EVALUATE

CALCULATETABLE (

    ADDCOLUMNS (

        TOPN ( 5, VALUES ( 'Product'[Product Name] ) ),

        "Position of red", SEARCH ( "red", 'Product'[Product Name], 1, BLANK () )

    ),

    'Product'[Color] IN { "Red", "Blue" }

)

```

| Product Name | Position of red |
| --- | --- |
| Contoso 512MB MP3 Player E51 Blue | (Blank) |
| Contoso 2G MP3 Player E200 Red | 28 |
| Contoso 2G MP3 Player E200 Blue | (Blank) |
| Contoso 4GB Flash MP3 Player E401 Blue | (Blank) |
| Contoso 8GB Super-Slim MP3/Video Player M800 Red | 46 |

```dax


--  SEARCH supports wildcards: ? and * to match any character or any

--  sequence of characters.

EVALUATE

CALCULATETABLE (

    ADDCOLUMNS (

        TOPN ( 5, VALUES ( 'Product'[Product Name] ) ),

        "player*blue", SEARCH ( "player*blue", 'Product'[Product Name], 1, BLANK () )

    ),

    'Product'[Color] IN { "Red", "Blue" }

)

```

| Product Name | player\*blue |
| --- | --- |
| Contoso 512MB MP3 Player E51 Blue | 19 |
| Contoso 2G MP3 Player E200 Red | (Blank) |
| Contoso 2G MP3 Player E200 Blue | 16 |
| Contoso 4GB Flash MP3 Player E401 Blue | 23 |
| Contoso 8GB Super-Slim MP3/Video Player M800 Red | (Blank) |

## Related articles

Learn more about SEARCH in the following articles:

- [**From SQL to DAX: String Comparison**](https://www.sqlbi.com/articles/from-sql-to-dax-string-comparison/)

  In DAX string comparison requires you more attention than in SQL, for several reasons: DAX doesn’t offer the same set of features you have in SQL, a few text comparison functions in DAX are only case-sensitive and others only case-insensitive,… [» Read more](https://www.sqlbi.com/articles/from-sql-to-dax-string-comparison/)
- [**Currency conversion in Power BI reports**](https://www.sqlbi.com/articles/currency-conversion-in-power-bi-reports/)

  This article describes how to implement currency conversion for reporting purposes in Power BI. [» Read more](https://www.sqlbi.com/articles/currency-conversion-in-power-bi-reports/)
- [**Controlling Format Strings in Calculation Groups**](https://www.sqlbi.com/articles/controlling-format-strings-in-calculation-groups/)

  This article describes how to control format strings in calculation groups. Before starting, we suggest you read the previous articles in this series. [» Read more](https://www.sqlbi.com/articles/controlling-format-strings-in-calculation-groups/)
- [**Optimizing text search in DAX**](https://www.sqlbi.com/articles/optimizing-text-search-in-dax/)

  This article describes how to optimize a text search operation in DAX. This technique can improve the performance of Power BI reports that use the contains condition in the filter pane or the filter mode of the Smart Filter Pro custom visual. [» Read more](https://www.sqlbi.com/articles/optimizing-text-search-in-dax/)

## Related functions

Other related functions are:

- [[FIND]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/search-function-dax](https://docs.microsoft.com/en-us/dax/search-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
