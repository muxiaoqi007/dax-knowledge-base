---
title: "REMOVEFILTERS"
function: "removefilters"
category: "Filter"
url: "https://dax.guide/removefilters/"
source: "dax.guide"
重要度:
难度:
---

# REMOVEFILTERS DAX Function (Filter)

Clear filters from the specified tables or columns.

## Syntax

REMOVEFILTERS ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| TableNameOrColumnName | Optional | The name of an existing table or column. |
| ColumnName | Optional Repeatable | A column in the same base table. |

## Remarks

REMOVEFILTERS is an alias for [[ALL]], but it can be used only as a [[CALCULATE]] modifier and not as a table expression like [[ALL]].

[» 8 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


-- Filter Litware/Red

EVALUATE

CALCULATETABLE (

    SUMMARIZE ( 'Product', 'Product'[Category], 'Product'[Brand], 'Product'[Color] ),

    Product[Brand] = "Litware",

    Product[Color] = "Red"

)



-- Remove Red filter, as a result the filter is Litware only

EVALUATE

CALCULATETABLE (

    CALCULATETABLE (

        SUMMARIZE ( 'Product', 'Product'[Category], 'Product'[Brand], 'Product'[Color] ),

        REMOVEFILTERS ( 'Product'[Color] )

    ),

    Product[Brand] = "Litware",

    Product[Color] = "Red"

)





```

| Category | Brand | Color |
| --- | --- | --- |
| Home Appliances | Litware | Red |

| Category | Brand | Color |
| --- | --- | --- |
| TV and Video | Litware | Silver |
| Home Appliances | Litware | Silver |
| Home Appliances | Litware | Blue |
| Home Appliances | Litware | White |
| Home Appliances | Litware | Red |
| TV and Video | Litware | Black |
| Home Appliances | Litware | Black |
| Home Appliances | Litware | Green |
| Home Appliances | Litware | Orange |
| Home Appliances | Litware | Pink |
| Home Appliances | Litware | Yellow |
| Home Appliances | Litware | Purple |
| TV and Video | Litware | Brown |
| Home Appliances | Litware | Brown |
| Home Appliances | Litware | Grey |

```dax


-- Filter Litware/Red

EVALUATE

CALCULATETABLE (

    SUMMARIZE ( 'Product', 'Product'[Category], 'Product'[Brand], 'Product'[Color] ),

    Product[Brand] = "Litware",

    Product[Color] = "Red"

)



-- Remove all the filters from Product: as a result, both Litware and Red filters are removed

EVALUATE

CALCULATETABLE (

    CALCULATETABLE (

        SUMMARIZE ( 'Product', 'Product'[Category], 'Product'[Brand], 'Product'[Color] ),

        REMOVEFILTERS ( 'Product' )

    ),

    Product[Brand] = "Litware",

    Product[Color] = "Red"

)

```

## Related articles

Learn more about REMOVEFILTERS in the following articles:

- [**Managing “all” functions in DAX: ALL, ALLSELECTED, ALLNOBLANKROW, ALLEXCEPT**](https://www.sqlbi.com/articles/managing-all-functions-in-dax-all-allselected-allnoblankrow-allexcept/)

  This article provides a complete explanation of the behavior of the ALLxxx functions in DAX. When used as filters in CALCULATE, ALLxxx functions might display unexpected behaviors. [» Read more](https://www.sqlbi.com/articles/managing-all-functions-in-dax-all-allselected-allnoblankrow-allexcept/)
- [**Mark as Date table**](https://www.sqlbi.com/articles/mark-as-date-table/)

  Tabular models (including Power BI) require marking the Date table as a date table to get appropriate results with time intelligence calculations. This article explains why this setting is required. [» Read more](https://www.sqlbi.com/articles/mark-as-date-table/)
- [**USERELATIONSHIP in Calculated Columns**](https://www.sqlbi.com/articles/userelationship-in-calculated-columns/)

  USERELATIONSHIP lets you temporarily change which relationship is active. Even though USERELATIONSHIP is easy to work with in measures, it can be challenging and give you inaccurate results when used in calculated columns. In this article we describe the details… [» Read more](https://www.sqlbi.com/articles/userelationship-in-calculated-columns/)
- [**Computing MTD, QTD, YTD in Power BI for the current period**](https://www.sqlbi.com/articles/computing-mtd-qtd-ytd-in-power-bi-for-the-current-period/)

  This article describes how to use the DAX time intelligence calculations applied to the latest period available in the data, also known as the “current” period. [» Read more](https://www.sqlbi.com/articles/computing-mtd-qtd-ytd-in-power-bi-for-the-current-period/)
- [**Filter context in DAX explained visually**](https://www.sqlbi.com/articles/filter-context-in-dax-explained-visually/)

  This article describes the DAX filter context using a conceptual model based on a visual representation. [» Read more](https://www.sqlbi.com/articles/filter-context-in-dax-explained-visually/)
- [**Differences between ALL and ALLCROSSFILTERED**](https://www.sqlbi.com/articles/differences-between-all-and-allcrossfiltered/)

  This article describes the differences between the ALL and CROSSFILTERED functions in DAX. [» Read more](https://www.sqlbi.com/articles/differences-between-all-and-allcrossfiltered/)
- [**ALL vs ALLSELECTED vs ALLEXCEPT vs REMOVEFILTERS**](https://www.sqlbi.com/articles/all-vs-allselected-vs-allexcept-vs-removefilters/)

  DAX offers many functions to remove filters from the filter context. In this article, we analyze the differences among the most commonly-used functions. [» Read more](https://www.sqlbi.com/articles/all-vs-allselected-vs-allexcept-vs-removefilters/)
- [**Using REMOVEFILTERS in DAX user-defined functions**](https://www.sqlbi.com/articles/using-removefilters-in-dax-user-defined-functions/)

  In this article, we implement a function that removes filter-keep column filters from a calendar, using REMOVEFILTERS as the return value of the function. [» Read more](https://www.sqlbi.com/articles/using-removefilters-in-dax-user-defined-functions/)

## Related functions

Other related functions are:

- [[ALL]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/removefilters-function-dax](https://docs.microsoft.com/en-us/dax/removefilters-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
