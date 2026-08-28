---
title: "TOPN"
function: "topn"
category: "Table manipulation"
url: "https://dax.guide/topn/"
source: "dax.guide"
重要度:
难度:
---

# TOPN DAX Function (Table manipulation)

Returns a given number of top rows according to a specified expression.

## Syntax

TOPN ( <N\_Value>, <Table> [, <OrderBy\_Expression> [, [<Order>] [, <OrderBy\_Expression> [, [<Order>] [, … ] ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| N\_Value |  | The number of rows to return. |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | An expression that defines the table from which rows are to be returned. || OrderBy\_Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression | Optional Repeatable | Expression to be used for sorting the table. |
| Order | Optional Repeatable | The order to be applied. 0/FALSE/DESC – descending; 1/TRUE/ASC – ascending. |

## Return values

Table An entire table or a table with one or more columns.

A table with the top N\_value rows of Table or an empty table if N\_value is 0 (zero) or less.

## Remarks

If there is a tie, in OrderBy\_Expression values, at the N-th row of the table, then all tied rows are returned. Then, when there are ties at the N-th row the function might return more than n rows.

If N\_Value is 0 (zero) or less than 0, then TOPN returns an empty table.

TOPN does not guarantee any sort order for the results.

[» 7 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  TOPN retrieves the top N items from a table after sorting

--  them by the result of the third argument.

--  Multiple sorting criteria can be provided in further parameters.

EVALUATE

    TOPN ( 

        3, 

        ADDCOLUMNS ( 

            VALUES ( 'Product'[Product Name] ),

            "@Sales Amount", [Sales Amount]

        ),

        [@Sales Amount],

        DESC

    )

ORDER BY [@Sales Amount] DESC

```

| Product Name | @Sales Amount |
| --- | --- |
| Adventure Works 26″ 720p LCD HDTV M140 Silver | 1,303,983.46 |
| A. Datum SLR Camera X137 Grey | 725,840.28 |
| Contoso Telephoto Conversion Lens X400 Silver | 683,779.95 |

```dax


--  TOPN might return more than the requested rows in presence of ties.

EVALUATE

    TOPN ( 

        3, 

        ADDCOLUMNS ( 

            VALUES ( 'Product'[Product Name] ),

            "@Sales Amount", MROUND ( [Sales Amount], 500000 )

        ),

        [@Sales Amount],

        DESC

    )

ORDER BY [@Sales Amount] DESC



--  Multiple sorting criteria can be provided in further parameters.

EVALUATE

    TOPN ( 

        3, 

        ADDCOLUMNS ( 

            VALUES ( 'Product'[Product Name] ),

            "@Sales Amount", MROUND ( [Sales Amount], 500000 )

        ),

        [@Sales Amount],

        DESC,

        [Product Name], 

        ASC

    )

ORDER BY [@Sales Amount] DESC

```

| Product Name | @Sales Amount |
| --- | --- |
| Adventure Works 26″ 720p LCD HDTV M140 Silver | 1500000 |
| Contoso Projector 1080p X980 White | 500000 |
| SV 16xDVD M360 Black | 500000 |
| A. Datum SLR Camera X137 Grey | 500000 |
| Contoso Telephoto Conversion Lens X400 Silver | 500000 |

| Product Name | @Sales Amount |
| --- | --- |
| Adventure Works 26″ 720p LCD HDTV M140 Silver | 1500000 |
| Contoso Projector 1080p X980 White | 500000 |
| A. Datum SLR Camera X137 Grey | 500000 |

## Related articles

Learn more about TOPN in the following articles:

- [**Displaying filter context in Power BI Tooltips**](https://www.sqlbi.com/articles/displaying-filter-context-in-power-bi-tooltips/)

  This article describes how to display the filter context applied to a calculation using a special DAX measure in Power BI Tooltips. [» Read more](https://www.sqlbi.com/articles/displaying-filter-context-in-power-bi-tooltips/)
- [**Table and column references using DAX variables**](https://www.sqlbi.com/articles/table-and-column-references-using-dax-variables/)

  This article describes how to correctly use column references when manipulating tables assigned to DAX variables, avoiding syntax errors and making the code easier to read and maintain. [» Read more](https://www.sqlbi.com/articles/table-and-column-references-using-dax-variables/)
- [**Filtering the Top 3 products for each category in Power BI**](https://www.sqlbi.com/articles/filtering-the-top-3-products-for-each-category-in-power-bi/)

  This article describes different techniques to display the first three products for each category in Power BI. It includes considerations on how to adapt the technique to different models and requirements. [» Read more](https://www.sqlbi.com/articles/filtering-the-top-3-products-for-each-category-in-power-bi/)
- [**Showing the top 5 products and Other row**](https://www.sqlbi.com/articles/showing-the-top-5-products-and-others-row/)

  This article shows how to add an additional “other” row to the selection obtained using the Top N filter in a Power BI report. [» Read more](https://www.sqlbi.com/articles/showing-the-top-5-products-and-others-row/)
- [**Filtering the top products alongside the other products in Power BI**](https://www.sqlbi.com/articles/filtering-the-top-products-alongside-the-other-products-in-power-bi/)

  This article shows an optimized DAX technique to display the first N products for each category in Power BI, adding a row that aggregates the value for all the other products. The companion video introduces the scenario and the general approach, while this article offers further insights and optimization solutions. [» Read more](https://www.sqlbi.com/articles/filtering-the-top-products-alongside-the-other-products-in-power-bi/)
- [**Find the products in the top 10 every year with DAX**](https://www.sqlbi.com/articles/find-the-products-in-the-top-10-every-year-with-dax/)

  This article outlines the process of creating a measure to identify the top 10 products by sales each year. [» Read more](https://www.sqlbi.com/articles/find-the-products-in-the-top-10-every-year-with-dax/)
- [**Debugging DAX variables using TOJSON and TOCSV**](https://www.sqlbi.com/articles/debugging-dax-variables-using-tojson-and-tocsv/)

  This article describes how to use the TOJSON and TOCSV functions to inspect the content of intermediate table variables when debugging a DAX measure. [» Read more](https://www.sqlbi.com/articles/debugging-dax-variables-using-tojson-and-tocsv/)

## Related functions

Other related functions are:

- [[TOPNSKIP]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/topn-function-dax](https://docs.microsoft.com/en-us/dax/topn-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
