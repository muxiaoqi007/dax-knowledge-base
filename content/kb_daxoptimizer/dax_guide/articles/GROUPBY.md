---
title: "GROUPBY"
function: "groupby"
category: "Table manipulation"
url: "https://dax.guide/groupby/"
source: "dax.guide"
重要度:
难度:
---

# GROUPBY DAX Function (Table manipulation)

Creates a summary the input table grouped by the specified columns.

## Syntax

GROUPBY ( <Table> [, <GroupBy\_ColumnName> [, [<Name>] [, [<Expression>] [, <GroupBy\_ColumnName> [, [<Name>] [, [<Expression>] [, … ] ] ] ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table |  | The input table. |
| GroupBy\_ColumnName | Optional Repeatable | A column to group by. |
| Name | Optional Repeatable | A column name to be added. |
| Expression  By Expression | Optional Repeatable | The expression of the new column. |

## Return values

Table An entire table or a table with one or more columns.

A table with the selected columns for the GroupBy\_columnName arguments and the grouped by columns designated by the name arguments.

## Remarks

Most of the times, [[SUMMARIZE]] can be used instead of GROUPBY.  
GROUPBY is required to aggregate the result of a column computed in a previous table expression.

[» 5 related articles](#articles)  
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

```dax


EVALUATE

VAR RoundedSales = 

    SELECTCOLUMNS (

        VALUES ( 'Product'[Product Name] ),

        "First Letter", LEFT ( 'Product'[Product Name], 1 ),

        "Rounded Sales", MROUND ( [Sales Amount], 10000 )

    )

VAR GroupByFirstLetter =

    GROUPBY (

        RoundedSales,

        [First Letter],

        "Sum Rounded Sales", SUMX ( CURRENTGROUP (), [Rounded Sales] )

    )

RETURN 

    GroupByFirstLetter

ORDER BY [First Letter]

```

| First Letter | Sum Rounded Sales |
| --- | --- |
| A | 6,080,000 |
| C | 7,050,000 |
| F | 5,520,000 |
| H | 30,000 |
| L | 3,160,000 |
| M | 160,000 |
| N | 1,000,000 |
| P | 2,460,000 |
| R | 0 |
| S | 1,270,000 |
| T | 1,060,000 |
| W | 1,860,000 |

## Related articles

Learn more about GROUPBY in the following articles:

- [**Nested grouping using GROUPBY vs SUMMARIZE**](https://www.sqlbi.com/articles/nested-grouping-using-groupby-vs-summarize/)

  DAX introduced a GROUPBY function that should replace SUMMARIZE in some scenarios. This article describes how to use GROUPBY in nested grouping scenarios and other improvements. [» Read more](https://www.sqlbi.com/articles/nested-grouping-using-groupby-vs-summarize/)
- [**Preparing a data model for Sankey Charts in Power BI**](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)

  This article describes how to correctly shape a data model and prepare data to use a Sankey Chart as a funnel, considering events related to a customer (contact, trial, subscription, renewal, and others). [» Read more](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)
- [**Differences between GROUPBY and SUMMARIZE**](https://www.sqlbi.com/articles/differences-between-groupby-and-summarize/)

  Both GROUPBY and SUMMARIZE are useful functions to group by columns. However, they differ in both performance and functionalities. Knowing the details lets developers choose the right function for their specific scenario. [» Read more](https://www.sqlbi.com/articles/differences-between-groupby-and-summarize/)
- [**Account receivable aging in Power BI**](https://www.sqlbi.com/articles/account-receivable-aging-in-power-bi/)

  This article describes an Accounts Receivable Aging report in Power BI, and shows how to simplify a business problem using existing modeling patterns. [» Read more](https://www.sqlbi.com/articles/account-receivable-aging-in-power-bi/)
- [**Find the products in the top 10 every year with DAX**](https://www.sqlbi.com/articles/find-the-products-in-the-top-10-every-year-with-dax/)

  This article outlines the process of creating a measure to identify the top 10 products by sales each year. [» Read more](https://www.sqlbi.com/articles/find-the-products-in-the-top-10-every-year-with-dax/)

## Related functions

Other related functions are:

- [[CURRENTGROUP]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/groupby-function-dax](https://docs.microsoft.com/en-us/dax/groupby-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
