---
title: "ISEMPTY"
function: "isempty"
category: "Information"
url: "https://dax.guide/isempty/"
source: "dax.guide"
重要度:
难度:
---

# ISEMPTY DAX Function (Information)

Returns true if the specified table or table-expression is Empty.

## Syntax

ISEMPTY ( <Table> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table |  | Table or table-expression. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

TRUE if the table is empty (has no rows), if else, FALSE.

[» 5 related articles](#articles)  

## Examples

```dax


--  ISEMPTY checks that a table contains no rows

--

--  This example retrieves the products with sales in 2007 and 

--  no sales in 2008 by using ISEMPTY

EVALUATE

VAR ProductsSales =

    ADDCOLUMNS (

        TOPN ( 10, VALUES ( 'Product'[Product Name] ) ),

        "HasSalesIn2007",

            NOT ISEMPTY (

                CALCULATETABLE ( Sales, 'Date'[Calendar Year] = "CY 2007"  )

            ),

        "NoSalesIn2008",

            ISEMPTY (

                CALCULATETABLE ( Sales,  'Date'[Calendar Year] = "CY 2008" )

            )

    )

VAR Result =

    FILTER (

        ProductsSales,

        [NoSalesIn2008] = TRUE && [HasSalesIn2007] = TRUE

    )

RETURN

    Result

```

## Related articles

Learn more about ISEMPTY in the following articles:

- [**Check Empty Table Condition with DAX**](https://www.sqlbi.com/articles/check-empty-table-condition-with-dax/)

  In DAX there are different ways to test whether a table is empty. This test can be used in complex DAX expressions and this short article briefly discuss what are the suggested approaches from a performance perspective. [» Read more](https://www.sqlbi.com/articles/check-empty-table-condition-with-dax/)
- [**Syncing slicers in Power BI**](https://www.sqlbi.com/articles/syncing-slicers-in-power-bi/)

  The June 2019 update of Power BI includes the ability to filter slicer items based on a measure. This article explains why this is an important feature that should replace bidirectional filters used for the same purpose. [» Read more](https://www.sqlbi.com/articles/syncing-slicers-in-power-bi/)
- [**Using tuple syntax in DAX expressions**](https://www.sqlbi.com/articles/using-tuple-syntax-in-dax-expressions/)

  This article describes the use of the tuple syntax in DAX expressions to simplify comparisons involving two or more columns. [» Read more](https://www.sqlbi.com/articles/using-tuple-syntax-in-dax-expressions/)
- [**Finding products without sales by using DAX**](https://www.sqlbi.com/articles/finding-products-without-sales-by-using-dax/)

  This article analyzes the performance of different DAX techniques to identify any products without sales in an area or a time period. [» Read more](https://www.sqlbi.com/articles/finding-products-without-sales-by-using-dax/)
- [**Show transaction details on the matrix visual in Power BI**](https://www.sqlbi.com/articles/show-transaction-details-on-the-matrix-visual-in-power-bi/)

  This article shows how to create a DAX measure that displays information from multiple columns in a business entity or transaction, into a single column of a matrix. [» Read more](https://www.sqlbi.com/articles/show-transaction-details-on-the-matrix-visual-in-power-bi/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/isempty-function-dax](https://docs.microsoft.com/en-us/dax/isempty-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
