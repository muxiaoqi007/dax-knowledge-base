---
title: "REPT"
function: "rept"
category: "Text"
url: "https://dax.guide/rept/"
source: "dax.guide"
重要度:
难度:
---

# REPT DAX Function (Text)

Repeats text a given number of times. Use REPT to fill a cell with a number of instances of a text string.

## Syntax

REPT ( <Text>, <NumberOfTimes> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Text |  | The text you want to repeat. |
| NumberOfTimes |  | A positive number specifying the number of times to repeat text. |

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

A string containing the changes.

## Remarks

If NumberOfTimes is 0 (zero), REPT returns an empty string.

If NumberOfTimes is not an integer, it is rounded to the closest integer.

The result of the REPT function cannot be longer than 32,767 characters, or REPT returns an error.

[» 2 related articles](#articles)  

## Examples

```dax


--  REPT repeats a string for a given number of times

DEFINE

    MEASURE Customer[Cars] =

        REPT ( "?", SELECTEDVALUE ( Customer[Cars Owned] ) )

EVALUATE

CALCULATETABLE (

    TOPN ( 8, 

        SUMMARIZECOLUMNS ( 

            Customer[Customer Name], 

            "Cars", [Cars]

        ),

        Customer[Customer Name], ASC

    ),

    Customer[Customer Name] <> "" 

)

ORDER BY Customer[Customer Name]

```

| Customer Name | Cars |
| --- | --- |
| Adams, Aaron | ? |
| Adams, Adam | ?? |
| Adams, Alex | ? |
| Adams, Alexandra | ? |
| Adams, Allison |  |
| Adams, Amanda | ? |
| Adams, Amber | ? |
| Adams, Andrea | ??? |

## Related articles

Learn more about REPT in the following articles:

- [**Handling customers with the same name in Power BI**](https://www.sqlbi.com/articles/handling-customers-with-the-same-name-in-power-bi/)

  This article explains how to show different customers with the same name in a Power BI report by using zero-width spaces, thus simplifying the presentation without adding visible characters to make the names unique. [» Read more](https://www.sqlbi.com/articles/handling-customers-with-the-same-name-in-power-bi/)
- [**Sorting duplicated names in a level of a hierarchy with DAX**](https://www.sqlbi.com/articles/sorting-duplicated-names-in-a-level-of-a-hierarchy-with-dax/)

  This article describes how to use DAX calculated columns to sort names that look like duplicates at a certain level of a hierarchy, but are unique when considering their full path within the hierarchy. [» Read more](https://www.sqlbi.com/articles/sorting-duplicated-names-in-a-level-of-a-hierarchy-with-dax/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/rept-function-dax](https://docs.microsoft.com/en-us/dax/rept-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
