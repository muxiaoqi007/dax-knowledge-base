---
title: "DETAILROWS"
function: "detailrows"
category: "Table manipulation"
url: "https://dax.guide/detailrows/"
source: "dax.guide"
重要度:
难度:
---

# DETAILROWS DAX Function (Table manipulation)

Returns the table data corresponding to the DetailRows expression defined on the specified Measure. If a DetailRows expression is not defined then the entire table to which the Measure belongs is returned.

## Syntax

DETAILROWS ( <Measure> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Measure |  | A Measure reference whose DetailRows expression is to be evaluated. |

## Return values

Table An entire table or a table with one or more columns.

## Remarks

**IMPORTANT** : DETAILROWS should have performed a context transition, but this design has not been implemented. As a workaround, if called in a row context, it should be wrapped in a [[CALCULATETABLE]] statement. Do not use in a row context if the intended behavior should not execute the context transition – assign the result of DETAILROWS in a variable before the iterator in that case.

[» 3 related articles](#articles)  

## Examples

Consider the following Detail Rows Expression in the Sales Amount measure:

```dax


SELECTCOLUMNS (

    Sales,

    "Order Number", Sales[Order Number],

    "Order Line Number", Sales[Order Line Number],

    "Customer", RELATED ( Customer[Name] ),

    "Product", RELATED ( 'Product'[Product Name] ),

    "Quantity", Sales[Quantity],

    "Line Amount", Sales[Quantity] * Sales[Net Price]

)

```

DETAILSROWS invokes the Detail Rows Expression for the corresponding measure in the same filter context.

```dax


-- Show DETAILROWS for transactions made in one day 

EVALUATE

CALCULATETABLE (

    DETAILROWS ( [Sales Amount] ),

    'Date'[Date] = DATE ( 2007, 9, 19 ),

    Customer[Customer Type] = "Person"

)

```

| Order Number | Order Line Number | Customer | Product | Quantity | Line Amount |
| --- | --- | --- | --- | --- | --- |
| 20070919123935 | 1 | Barnes, Alexis | Contoso 512MB MP3 Player E51 Blue | 1 | 11.69 |
| 20070919123937 | 1 | Ruiz, Eddie | Contoso 512MB MP3 Player E51 Blue | 1 | 11.69 |
| 20070919123941 | 1 | Anderson, Eduardo | Contoso 512MB MP3 Player E51 Blue | 1 | 11.69 |
| 20070919427828 | 1 | Gao, Ernest | Fabrikam Social Videographer 2/3” 17mm E100 Grey | 1 | 144.00 |
| 20070919712464 | 1 | Cook, Jared | MGS Rise of Nations2009 E152 | 1 | 38.70 |
| 20070919712465 | 1 | Patel, Cassandra | MGS Rise of Nations2009 E152 | 1 | 38.70 |
| 20070919712466 | 1 | He, Willie | MGS Rise of Nations2009 E152 | 1 | 38.70 |
| 20070919712467 | 1 | Jones, Jennifer | MGS Rise of Nations2009 E152 | 1 | 38.70 |
| 20070919525616 | 1 | Rubio, Jésus | The Phone Company Touch Screen Phones – CRT M11 Grey | 1 | 170.10 |
| 20070919525617 | 1 | Shan, Leonard | The Phone Company Touch Screen Phones – CRT M11 Grey | 1 | 170.10 |
| 20070919525618 | 1 | Hernandez, Albert | The Phone Company Touch Screen Phones – CRT M11 Grey | 1 | 170.10 |
| 20070919123936 | 1 | Vazquez, Monique | Contoso 512MB MP3 Player E51 Blue | 4 | 46.76 |
| 20070919525615 | 1 | Hall, Destiny | The Phone Company Touch Screen Phones – CRT M11 Grey | 4 | 680.40 |

## Related articles

Learn more about DETAILROWS in the following articles:

- [**Controlling drillthrough using Detail Rows Expressions in DAX**](https://www.sqlbi.com/articles/controlling-drillthrough-using-detail-rows-expressions-in-dax/)

  The Detail Rows Expression in a Tabular model provides the user with control over the drillthrough results obtained by showing details of a measure. This article describes typical DAX expressions you can use in this property. [» Read more](https://www.sqlbi.com/articles/controlling-drillthrough-using-detail-rows-expressions-in-dax/)
- [**Creating table functions in DAX using DETAILROWS**](https://www.sqlbi.com/articles/creating-table-functions-in-dax-using-detailrows/)

  This article describes how to use the detail rows expression of a measure to obtain the equivalent of creating table functions in DAX. This allows the reusing of a table expression in multiple CALCULATE filters. [» Read more](https://www.sqlbi.com/articles/creating-table-functions-in-dax-using-detailrows/)
- [**Controlling drillthrough in Excel PivotTables connected to Power BI or Analysis Services**](https://www.sqlbi.com/articles/controlling-drillthrough-in-excel-pivottables-connected-to-power-bi-or-analysis-services/)

  This article describes how to customize the drillthrough experience in Excel PivotTables connected to Power BI datasets or Analysis Services databases. [» Read more](https://www.sqlbi.com/articles/controlling-drillthrough-in-excel-pivottables-connected-to-power-bi-or-analysis-services/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/detailrows-function-dax](https://docs.microsoft.com/en-us/dax/detailrows-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
