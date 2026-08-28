---
title: "ALLCROSSFILTERED"
function: "allcrossfiltered"
category: "Filter"
url: "https://dax.guide/allcrossfiltered/"
source: "dax.guide"
重要度:
难度:
---

# ALLCROSSFILTERED DAX Function (Filter)

Clear all filters which are applied to the specified table.

## Syntax

ALLCROSSFILTERED ( <TableName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| TableName |  | The table that you want to clear filters on. |

## Remarks

ALLCROSSFILTERED can be used only as a [[CALCULATE]] modifier and cannot be used as a table function.

ALLCROSSFILTERED removes all the filters on an expanded table (like [[ALL]]) and on columns and tables that are cross-filtering the table argument because of [limited (weak) relationships](https://www.sqlbi.com/articles/strong-and-weak-relationships-in-power-bi/) and/or bidirectional cross-filters set on relationships directly or indirectly connected to the expanded table.

[» 1 related article](#articles)  

## Examples

ALLCROSSFILTERED removes the filter from the ColorGroups table that reaches Product through a limited relationship (many-to-many cardinality):

```dax


DEFINE

    MEASURE Product[#Prods] =

        COUNTROWS ( 'Product' )

    MEASURE Product[#AllProds] =

        CALCULATE ( [#Prods], ALL ( 'Product' ) )

    MEASURE Product[#AllCrossProds] =

        CALCULATE ( [#Prods], ALLCROSSFILTERED ( 'Product' ) )

EVALUATE

ADDCOLUMNS (

    VALUES ( ColorGroups[Group] ),

    "#Prods", [#Prods],

    "#AllProds", [#AllProds],

    "#AllCrossProds", [#AllCrossProds]

)

```

| Group | #Prods | #AllProds | #AllCrossProds |
| --- | --- | --- | --- |
| 2009-01-01 | 135 | 135 | 2,517 |
| 2010-01-01 | 236 | 236 | 2,517 |

## Related articles

Learn more about ALLCROSSFILTERED in the following articles:

- [**Differences between ALL and ALLCROSSFILTERED**](https://www.sqlbi.com/articles/differences-between-all-and-allcrossfiltered/)

  This article describes the differences between the ALL and CROSSFILTERED functions in DAX. [» Read more](https://www.sqlbi.com/articles/differences-between-all-and-allcrossfiltered/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Renato Lira

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/allcrossfiltered-function-dax](https://docs.microsoft.com/en-us/dax/allcrossfiltered-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
