---
title: "ISONORAFTER"
function: "isonorafter"
category: "Information"
url: "https://dax.guide/isonorafter/"
source: "dax.guide"
重要度:
难度:
---

# ISONORAFTER DAX Function (Information)

The IsOnOrAfter function is a boolean function that emulates the behavior of Start At clause and returns true for a row that meets all the conditions mentioned as parameters in this function.

## Syntax

ISONORAFTER ( <Value1>, <Value2> [, [<Order>] [, <Value1>, <Value2> [, [<Order>] [, … ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Value1 | Repeatable | Expression to be compared with second parameter. |
| Value2 | Repeatable | Expression to be compared with first parameter. |
| Order | Optional Repeatable | The order to be applied. 0/FALSE/DESC – descending; 1/TRUE/ASC – ascending. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

Returns TRUE when the set of values passed as arguments in Value1 is “greater than or equal to” the set of values passed as arguments in Value2.

## Remarks

The ISONORAFTER function emulates the behavior of a START AT clause in EVALUATE, and returns TRUE when all of the values passed as argument meet the condition specified.  
Usually this function is evaluated in a filter condition during an iteration, applying it to the current row context. However, ISONORAFTER uses the existing evaluation context, so any row context must be created outside of ISONORAFTER, which is not an iterator.

A blank value is matched with [[BLANK]](), in this comparison a blank and an empty string are considered as different values and an empty string is after a blank in ascending order.

[» 1 related article](#articles)  
[» 1 related function](#alt)  

## Examples

The following query filters the months greater than or equal to October 2008 by using the ISONORAFTER function in the filter condition of FILTER.

```dax


EVALUATE

FILTER (

    SUMMARIZE (

        'Date',

        'Date'[Calendar Year],

        'Date'[Month],

        'Date'[Month Number]

    ),

    ISONORAFTER (

        'Date'[Calendar Year], "CY 2008", ASC,

        'Date'[Month Number], 10, ASC

    )

)

ORDER BY

    'Date'[Calendar Year],

    'Date'[Month Number]

```

```dax


--  ISAFTER and ISONORAFTER are useful functions to compare multiple columns

--  A column is compared only if the previous columns have the same value

EVALUATE

ADDCOLUMNS (

    SUMMARIZE ( 'Product', 'Product'[Category], 'Product'[Subcategory] ),

    "OnOrAfterCameraAndCamcorders",

        ISONORAFTER (

            'Product'[Category], "Cameras and camcorders", ASC,

            'Product'[Subcategory], "Digital Cameras", ASC

        ),

    "AfterCameraAndCamcorders",

        ISAFTER (

            'Product'[Category], "Cameras and camcorders", ASC,

            'Product'[Subcategory], "Digital Cameras", ASC )

)

ORDER BY

    'Product'[Category],

    'Product'[Subcategory]

```

| Category | Subcategory | OnOrAfterCameraAndCamcorders | AfterCameraAndCamcorders |
| --- | --- | --- | --- |
| Audio | Bluetooth Headphones | false | false |
| Audio | MP4&MP3 | false | false |
| Audio | Recording Pen | false | false |
| Cameras and camcorders | Camcorders | false | false |
| Cameras and camcorders | Cameras & Camcorders Accessories | false | false |
| Cameras and camcorders | Digital Cameras | true | false |
| Cameras and camcorders | Digital SLR Cameras | true | true |
| Cell phones | Cell phones Accessories | true | true |
| Cell phones | Home & Office Phones | true | true |
| Cell phones | Smart phones & PDAs | true | true |
| Cell phones | Touch Screen Phones | true | true |
| Computers | Computers Accessories | true | true |
| Computers | Desktops | true | true |
| Computers | Laptops | true | true |
| Computers | Monitors | true | true |
| Computers | Printers, Scanners & Fax | true | true |
| Computers | Projectors & Screens | true | true |
| Games and Toys | Boxed Games | true | true |
| Games and Toys | Download Games | true | true |
| Home Appliances | Air Conditioners | true | true |
| Home Appliances | Coffee Machines | true | true |
| Home Appliances | Fans | true | true |
| Home Appliances | Lamps | true | true |
| Home Appliances | Microwaves | true | true |
| Home Appliances | Refrigerators | true | true |
| Home Appliances | Washers & Dryers | true | true |
| Home Appliances | Water Heaters | true | true |
| Music, Movies and Audio Books | Movie DVD | true | true |
| TV and Video | Car Video | true | true |
| TV and Video | Home Theater System | true | true |
| TV and Video | Televisions | true | true |
| TV and Video | VCD & DVD | true | true |

## Related articles

Learn more about ISONORAFTER in the following articles:

- [**Numbering sequence of events in DAX**](https://www.sqlbi.com/articles/numbering-sequence-of-events-in-dax/)

  This article explains how to create a calculated column in DAX to get a sequential number identifying the events related to a particular entity. [» Read more](https://www.sqlbi.com/articles/numbering-sequence-of-events-in-dax/)

## Related functions

Other related functions are:

- [[ISAFTER]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/isonorafter-function-dax](https://docs.microsoft.com/en-us/dax/isonorafter-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
