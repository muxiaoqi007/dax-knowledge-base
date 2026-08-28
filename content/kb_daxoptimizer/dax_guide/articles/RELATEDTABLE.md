---
title: "RELATEDTABLE"
function: "relatedtable"
category: "Relationship management"
url: "https://dax.guide/relatedtable/"
source: "dax.guide"
重要度:
难度:
---

# RELATEDTABLE DAX Function (Relationship management) Context Transition

Returns the related tables filtered so that it only includes the related rows.

## Syntax

RELATEDTABLE ( <Table> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table |  | The table that contains the desired value. |

## Return values

Table An entire table or a table with one or more columns.

A table of values.

## Remarks

The RELATEDTABLE function performs a context transition from row context(s) to a filter context, and evaluates the expression in the resulting filter context.  
This function is a shortcut for [[CALCULATETABLE]] function with no additional filters, accepting only a table reference and not a table expression.

[» 4 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  RELATEDTABLE is an alias - albeit limited - of CALCULATETABLE

DEFINE

    MEASURE Sales[Sales Amount] =

        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

    MEASURE Sales[TX/Customer w/rel] =

        AVERAGEX ( Customer, COUNTROWS ( RELATEDTABLE ( Sales ) ) )

    MEASURE Sales[TX/Customer w/calc] =

        AVERAGEX ( Customer, COUNTROWS ( CALCULATETABLE ( Sales ) ) )



EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Calendar Year],

    "Sales Amount", [Sales Amount],

    "TX/Customer w/rel", [TX/Customer w/rel],

    "TX/Customer w/calc", [TX/Customer w/calc]

)



```

| Calendar Year | Sales Amount | TX/Customer w/rel | TX/Customer w/calc |
| --- | --- | --- | --- |
| CY 2007 | 11,309,946.12 | 3.96 | 3.96 |
| CY 2008 | 9,927,582.99 | 8.23 | 8.23 |
| CY 2009 | 9,353,814.87 | 14.55 | 14.55 |

## Related articles

Learn more about RELATEDTABLE in the following articles:

- [**Row Context and Filter Context in DAX**](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)

  Understanding the difference between row context and filter context is important in using DAX correctly. This article introduces these two concepts. [» Read more](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)
- [**Understanding Context Transition**](https://www.sqlbi.com/articles/understanding-context-transition/)

  The context transition in DAX is the transformation of row contexts into an equivalent filter context performed by CALCULATE and CALCULATETABLE. Managing this behavior is the next step in learning DAX once you understand row context and filter context. This article provides the basics of context transition. [» Read more](https://www.sqlbi.com/articles/understanding-context-transition/)
- [**Using join functions in DAX**](https://www.sqlbi.com/articles/using-join-functions-in-dax/)

  This article describes the practical uses of NATURALLEFTOUTERJOIN and NATURALINNERJOIN in DAX. These functions are not commonly used in DAX because they do not have the same flexibility as the corresponding concepts in SQL. [» Read more](https://www.sqlbi.com/articles/using-join-functions-in-dax/)
- [**Using RELATED and RELATEDTABLE in DAX**](https://www.sqlbi.com/articles/using-related-and-relatedtable-in-dax/)

  RELATED and its companion function RELATEDTABLE, are two common DAX functions that are required when using a row context with relationships. In this article we describe why and when to use these two functions. [» Read more](https://www.sqlbi.com/articles/using-related-and-relatedtable-in-dax/)

## Related functions

Other related functions are:

- [[CALCULATETABLE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Pär Adeen

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/relatedtable-function-dax](https://docs.microsoft.com/en-us/dax/relatedtable-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
