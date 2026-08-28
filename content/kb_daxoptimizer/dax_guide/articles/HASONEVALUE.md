---
title: "HASONEVALUE"
function: "hasonevalue"
category: "Information"
url: "https://dax.guide/hasonevalue/"
source: "dax.guide"
重要度:
难度:
---

# HASONEVALUE DAX Function (Information)

Returns true when there’s only one value in the specified column.

## Syntax

HASONEVALUE ( <ColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName |  | The column to check the filter info. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

TRUE when the context for ColumnName has been filtered down to one distinct value only. Otherwise is FALSE.

## Remarks

HASONEVALUE corresponds to the following code:

```dax


COUNTROWS ( VALUES ( <ColumnName> ) ) = 1

```

[» 3 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  HASONEVALUE checks that a column has only one value 

--  visible in the current filter context

DEFINE

    MEASURE Sales[Audio only] =

        CALCULATE (

            HASONEVALUE ( 'Product'[Category] ),

            'Product'[Category] = "Audio"

        )

    MEASURE Sales[Audio and computers] =

        CALCULATE (

            HASONEVALUE ( 'Product'[Category] ),

            'Product'[Category] IN { "Audio", "Computers" }

        )

    MEASURE Sales[Audio and bananas] =

        CALCULATE (

            HASONEVALUE ( 'Product'[Category] ),

            'Product'[Category] IN { "Audio", "Bananas" }

        )

EVALUATE

{

     ( "Audio only", [Audio only] ),

     ( "Audio and computers", [Audio and computers] ),

     ( "Audio and bananas", [Audio and bananas] )

}

```

| Value1 | Value2 |
| --- | --- |
| Audio only | true |
| Audio and computers | false |
| Audio and bananas | true |

## Related articles

Learn more about HASONEVALUE in the following articles:

- [**Using the SELECTEDVALUE function in DAX**](https://www.sqlbi.com/articles/using-the-selectedvalue-function-in-dax/)

  This article describes how the SELECTEDVALUE DAX function simplifies the syntax required in many scenarios where you need to read a single value selected in the filter context. [» Read more](https://www.sqlbi.com/articles/using-the-selectedvalue-function-in-dax/)
- [**Distinguishing HASONEVALUE from ISINSCOPE**](https://www.sqlbi.com/articles/distinguishing-hasonevalue-from-isinscope/)

  This article describes the differences between HASONEVALUE and ISINSCOPE, which are two useful DAX functions to control the filters and the grouping that are active in a report. [» Read more](https://www.sqlbi.com/articles/distinguishing-hasonevalue-from-isinscope/)
- [**Understanding Group By Columns in Power BI**](https://www.sqlbi.com/articles/understanding-group-by-columns-in-power-bi/)

  This article describes how Power BI uses the Group By Columns attribute of a column and how you can leverage it in specific scenarios. [» Read more](https://www.sqlbi.com/articles/understanding-group-by-columns-in-power-bi/)

## Related functions

Other related functions are:

- [[HASONEFILTER]]
- [[VALUES]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/hasonevalue-function-dax](https://docs.microsoft.com/en-us/dax/hasonevalue-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
