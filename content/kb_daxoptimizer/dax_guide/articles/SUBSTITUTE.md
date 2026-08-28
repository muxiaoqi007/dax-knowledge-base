---
title: "SUBSTITUTE"
function: "substitute"
category: "Text"
url: "https://dax.guide/substitute/"
source: "dax.guide"
重要度:
难度:
---

# SUBSTITUTE DAX Function (Text)

Replaces existing text with new text in a text string.

## Syntax

SUBSTITUTE ( <Text>, <OldText>, <NewText> [, <InstanceNumber>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Text |  | A string of text, or a reference to a cell containing text, in which you want to substitute characters. |
| OldText |  | The existing text you want to replace. If the case of old\_text does not match the case in the existing text, SUBSTITUTE will not replace the text. |
| NewText |  | The text you want to replace old\_text with. |
| InstanceNumber | Optional | The occurrence of old\_text you want to replace. If omitted, every instance of old\_text is replaced. |

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

The modified string.

## Remarks

Use the SUBSTITUTE function to replace specific text in a text string; use the [[REPLACE]] function to replace any text of variable length that occurs in a specific location in a text string.

The SUBSTITUTE function is case-sensitive. If case does not match between Text and OldText, SUBSTITUTE will not replace the text.

[» 1 related article](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  SUBSTITUTE replaces a substring in a string with another value.

EVALUATE

CALCULATETABLE (

    ADDCOLUMNS (

        TOPN ( 5, VALUES ( 'Product'[Product Name] ) ),

        "New name", 

            SUBSTITUTE ( 

                'Product'[Product Name], 

                "MP3 Player",

                "Zune-like player" 

            )

    ),

    'Product'[Color] IN { "Red", "Blue" }

)

```

| Product Name | New name |
| --- | --- |
| Contoso 512MB MP3 Player E51 Blue | Contoso 512MB Zune-like player E51 Blue |
| Contoso 2G MP3 Player E200 Red | Contoso 2G Zune-like player E200 Red |
| Contoso 2G MP3 Player E200 Blue | Contoso 2G Zune-like player E200 Blue |
| Contoso 4GB Flash MP3 Player E401 Blue | Contoso 4GB Flash Zune-like player E401 Blue |
| Contoso 8GB Super-Slim MP3/Video Player M800 Red | Contoso 8GB Super-Slim MP3/Video Player M800 Red |

```dax


--  SUBSTITUTE replaces a substring in a string with another value.

EVALUATE

CALCULATETABLE (

    ADDCOLUMNS (

        TOPN ( 5, VALUES ( 'Product'[Product Name] ) ),

        "Hide 2",

            SUBSTITUTE ( 

                'Product'[Product Name], 

                "2", "?" 

            )

    ),

    'Product'[Color] IN { "Red", "Blue" }

)

```

| Product Name | Hide 2 |
| --- | --- |
| Contoso 512MB MP3 Player E51 Blue | Contoso 51?MB MP3 Player E51 Blue |
| Contoso 2G MP3 Player E200 Red | Contoso ?G MP3 Player E?00 Red |
| Contoso 2G MP3 Player E200 Blue | Contoso ?G MP3 Player E?00 Blue |
| Contoso 4GB Flash MP3 Player E401 Blue | Contoso 4GB Flash MP3 Player E401 Blue |
| Contoso 8GB Super-Slim MP3/Video Player M800 Red | Contoso 8GB Super-Slim MP3/Video Player M800 Red |

## Related articles

Learn more about SUBSTITUTE in the following articles:

- [**From SQL to DAX: String Comparison**](https://www.sqlbi.com/articles/from-sql-to-dax-string-comparison/)

  In DAX string comparison requires you more attention than in SQL, for several reasons: DAX doesn’t offer the same set of features you have in SQL, a few text comparison functions in DAX are only case-sensitive and others only case-insensitive,… [» Read more](https://www.sqlbi.com/articles/from-sql-to-dax-string-comparison/)

## Related functions

Other related functions are:

- [[REPLACE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/substitute-function-dax](https://docs.microsoft.com/en-us/dax/substitute-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
