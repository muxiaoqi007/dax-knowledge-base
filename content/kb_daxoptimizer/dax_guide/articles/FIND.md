---
title: "FIND"
function: "find"
category: "Text"
url: "https://dax.guide/find/"
source: "dax.guide"
重要度:
难度:
---

# FIND DAX Function (Text)

Returns the starting position of one text string within another text string. FIND is case-sensitive and accent-sensitive.

## Syntax

FIND ( <FindText>, <WithinText> [, <StartPosition>] [, <NotFoundValue>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| FindText |  | The text you want to find. Use double quotes (empty text) to match the first character in within\_text; wildcard characters not allowed. |
| WithinText |  | The text containing the text you want to find. |
| StartPosition | Optional | The character at which to start the search; if omitted, StartPosition = 1. The first character in WithinText is character number 1. |
| NotFoundValue | Optional | The numeric value to be returned if the text is not found; if omitted, an error is returned. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

Number that shows the starting point of the text string you want to find.

## Remarks

FIND does not support wildcards. To use wildcards, use [[SEARCH]].

[» 2 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  FIND searches the position of a substring inside a string.

--  Comparison is case-sensitive.

--  You can provide the position from where to start searching

--  and a default value to return in case of no-match.

--  If no default value is provided, FIND raises an error in case 

--  the substring is not found.

EVALUATE

CALCULATETABLE (

    ADDCOLUMNS (

        TOPN ( 5, VALUES ( 'Product'[Product Name] ) ),

        "Position of Red", FIND ( "Red", 'Product'[Product Name], 1, BLANK () )

    ),

    'Product'[Color] IN { "Red", "Blue" }

)

```

| Product Name | Position of Red |
| --- | --- |
| Contoso 512MB MP3 Player E51 Blue | (Blank) |
| Contoso 2G MP3 Player E200 Red | 28 |
| Contoso 2G MP3 Player E200 Blue | (Blank) |
| Contoso 4GB Flash MP3 Player E401 Blue | (Blank) |
| Contoso 8GB Super-Slim MP3/Video Player M800 Red | 46 |

## Related articles

Learn more about FIND in the following articles:

- [**From SQL to DAX: String Comparison**](https://www.sqlbi.com/articles/from-sql-to-dax-string-comparison/)

  In DAX string comparison requires you more attention than in SQL, for several reasons: DAX doesn’t offer the same set of features you have in SQL, a few text comparison functions in DAX are only case-sensitive and others only case-insensitive,… [» Read more](https://www.sqlbi.com/articles/from-sql-to-dax-string-comparison/)
- [**Optimizing text search in DAX**](https://www.sqlbi.com/articles/optimizing-text-search-in-dax/)

  This article describes how to optimize a text search operation in DAX. This technique can improve the performance of Power BI reports that use the contains condition in the filter pane or the filter mode of the Smart Filter Pro custom visual. [» Read more](https://www.sqlbi.com/articles/optimizing-text-search-in-dax/)

## Related functions

Other related functions are:

- [[SEARCH]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/find-function-dax](https://docs.microsoft.com/en-us/dax/find-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
