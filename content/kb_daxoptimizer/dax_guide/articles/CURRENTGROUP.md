---
title: "CURRENTGROUP"
function: "currentgroup"
category: "Table manipulation"
url: "https://dax.guide/currentgroup/"
source: "dax.guide"
重要度:
难度:
---

# CURRENTGROUP DAX Function (Table manipulation)

Access to the (sub)table representing current group in GroupBy function. Can be used only inside GroupBy function.

## Syntax

CURRENTGROUP ( )

This expression has no parameters.

## Return values

Table An entire table or a table with one or more columns.

Returns a set of rows from the “table” argument of [[GROUPBY]] that belong to the current row of the [[GROUPBY]] result.

## Remarks

The CURRENTGROUP function takes no arguments and is only supported as the first argument to one of the following aggregation functions: AverageX, CountAX, CountX, GeoMeanX, MaxX, MinX, ProductX, StDevX.S, StDevX.P, SumX, VarX.S, VarX.P .

[» 4 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  GROUPBY is useful to group by columns with no lineage

--  Each column added by GROUPBY must iterate CURRENTGROUP(). 

--  Moreover, you cannot use CALCULATE inside

--  a GROUPBY iteration.

DEFINE

VAR AverageCustomerSales =

    AVERAGEX ( Customer, [Sales Amount] )

VAR TaggedCustomers =

    SUMMARIZECOLUMNS (

        Customer[CustomerKey],

        "Customer Category",

            IF ( [Sales Amount] >= AverageCustomerSales, "Above Average", "Below Average" )

    )

VAR Result =

    GROUPBY (

        TaggedCustomers,

        [Customer Category],

        "# Customers", COUNTX ( CURRENTGROUP (), 1 )

    )

EVALUATE

    Result

```

| Customer Category | # Customers |
| --- | --- |
| Below Average | 18,062 |
| Above Average | 807 |

```dax


DEFINE

VAR CustomersAndCategories = 

    SUMMARIZE ( Sales, Customer[CustomerKey], 'Product'[Category] )

VAR CustomersWithNumCategories = 

    GROUPBY ( 

        CustomersAndCategories, 

        'Product'[Category],

        "@Customers", SUMX ( CURRENTGROUP(), 1 )

    )

EVALUATE

    CustomersWithNumCategories

ORDER BY 

    'Product'[Category]

```

| Category | @Customers |
| --- | --- |
| Audio | 997 |
| Cameras and camcorders | 1,873 |
| Cell phones | 552 |
| Computers | 2,088 |
| Games and Toys | 5,785 |
| Home Appliances | 1,946 |
| Music, Movies and Audio Books | 377 |
| TV and Video | 3,421 |

## Related articles

Learn more about CURRENTGROUP in the following articles:

- [**Nested grouping using GROUPBY vs SUMMARIZE**](https://www.sqlbi.com/articles/nested-grouping-using-groupby-vs-summarize/)

  DAX introduced a GROUPBY function that should replace SUMMARIZE in some scenarios. This article describes how to use GROUPBY in nested grouping scenarios and other improvements. [» Read more](https://www.sqlbi.com/articles/nested-grouping-using-groupby-vs-summarize/)
- [**Preparing a data model for Sankey Charts in Power BI**](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)

  This article describes how to correctly shape a data model and prepare data to use a Sankey Chart as a funnel, considering events related to a customer (contact, trial, subscription, renewal, and others). [» Read more](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)
- [**Differences between GROUPBY and SUMMARIZE**](https://www.sqlbi.com/articles/differences-between-groupby-and-summarize/)

  Both GROUPBY and SUMMARIZE are useful functions to group by columns. However, they differ in both performance and functionalities. Knowing the details lets developers choose the right function for their specific scenario. [» Read more](https://www.sqlbi.com/articles/differences-between-groupby-and-summarize/)
- [**Find the products in the top 10 every year with DAX**](https://www.sqlbi.com/articles/find-the-products-in-the-top-10-every-year-with-dax/)

  This article outlines the process of creating a measure to identify the top 10 products by sales each year. [» Read more](https://www.sqlbi.com/articles/find-the-products-in-the-top-10-every-year-with-dax/)

## Related functions

Other related functions are:

- [[GROUPBY]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/currentgroup-function-dax](https://learn.microsoft.com/en-us/dax/currentgroup-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
