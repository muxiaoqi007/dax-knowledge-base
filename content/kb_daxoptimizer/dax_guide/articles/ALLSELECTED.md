---
title: "ALLSELECTED"
function: "allselected"
category: "Filter"
url: "https://dax.guide/allselected/"
source: "dax.guide"
重要度:
难度:
---

# ALLSELECTED DAX Function (Filter)

Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied inside the query, but keeping filters that come from outside.

## Syntax

ALLSELECTED ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| TableNameOrColumnName | Optional | Remove all filters on the specified table or column applied within the query. |
| ColumnName | Optional Repeatable | A column in the same base table. |

## Return values

Table An entire table or a table with one or more columns.

## Remarks

This function removes the corresponding filters from the filter context, restoring the last shadow filter context. It does not materialize the resulting table when called directly in a filter argument of [[CALCULATE]] or [[CALCULATETABLE]] .

ALLSELECTED can be used as a table expression when it has at least one argument.  
ALLSELECTED without arguments can be used only as a [[CALCULATE]] or [[CALCULATETABLE]] modifier.

ALLSELECTED supports multiple columns as argument since May 2019.  
In former versions this syntax is equivalent of ALLSELECTED ( table[column1], table[column2] ):

```dax


SUMMARIZE (

    ALLSELECTED ( table ),

    table[column1],

    table[column2]

)

```

[» 9 related articles](#articles)  
[» 3 related functions](#alt)  

## Examples

ALLSELECTED is typically used as a CALCULATE modifier.

```dax


CALCULATE ( ..., ALLSELECTED () )

CALCULATE ( ..., ALLSELECTED ( table ) )

CALCULATE ( ..., ALLSELECTED ( table[column] ) )

```

ALLSELECTED can be used as a table function, even though it is not a best practice.

```dax


EVALUATE

CALCULATETABLE (

    { COUNTROWS ( ALLSELECTED ( Product ) ) },

    Product[Category] = "Audio"

)

```

Be aware that using ALLSELECTED in an iterator could produce unintuitive results.

```dax


EVALUATE

CALCULATETABLE (

    ADDCOLUMNS (

        ALL ( 'Product'[Category] ),

        "Sales Amount", [Sales Amount],

        "Sales Sel",

            CALCULATE (

                [Sales Amount],

                ALLSELECTED ( Product[Category] )

            )

    ),

    Product[Category] IN { "Audio", "Computer" }

)

```

| Product[Category] | Sales Amount | Sales Sel |
| --- | --- | --- |
| Audio | 384,518.16 | 30,591,343.98 |
| TV and Video | 4,392,768.29 | 30,591,343.98 |
| Computers | 6,741,548.73 | 30,591,343.98 |
| Cameras and camcorders | 7,192,581.95 | 30,591,343.98 |
| Cell phones | 1,604,610.26 | 30,591,343.98 |
| Music, Movies and Audio Books | 314,206.74 | 30,591,343.98 |
| Games and Toys | 360,652.81 | 30,591,343.98 |
| Home Appliances | 9,600,457.04 | 30,591,343.98 |

## Related articles

Learn more about ALLSELECTED in the following articles:

- [**The definitive guide to ALLSELECTED**](https://www.sqlbi.com/articles/the-definitive-guide-to-allselected/)

  ALLSELECTED is a powerful function that can hide several traps. This article is an in-depth analysis of the behavior of ALLSELECTED, explaining shadow filter contexts, what they are and how they are used by ALLSELECTED. [» Read more](https://www.sqlbi.com/articles/the-definitive-guide-to-allselected/)
- [**Managing “all” functions in DAX: ALL, ALLSELECTED, ALLNOBLANKROW, ALLEXCEPT**](https://www.sqlbi.com/articles/managing-all-functions-in-dax-all-allselected-allnoblankrow-allexcept/)

  This article provides a complete explanation of the behavior of the ALLxxx functions in DAX. When used as filters in CALCULATE, ALLxxx functions might display unexpected behaviors. [» Read more](https://www.sqlbi.com/articles/managing-all-functions-in-dax-all-allselected-allnoblankrow-allexcept/)
- [**Computing same product sales in DAX**](https://www.sqlbi.com/articles/computing-same-product-sales-in-dax/)

  This article shows a technique in DAX to compute the sales volume of products that were available right from the beginning of a selected time period, ignoring products introduced afterwards. [» Read more](https://www.sqlbi.com/articles/computing-same-product-sales-in-dax/)
- [**Filtering the Top 3 products for each category in Power BI**](https://www.sqlbi.com/articles/filtering-the-top-3-products-for-each-category-in-power-bi/)

  This article describes different techniques to display the first three products for each category in Power BI. It includes considerations on how to adapt the technique to different models and requirements. [» Read more](https://www.sqlbi.com/articles/filtering-the-top-3-products-for-each-category-in-power-bi/)
- [**Introducing RANKX in DAX**](https://www.sqlbi.com/articles/introducing-rankx-in-dax/)

  RANKX is a simple function used to rank a value within a list of values. Its use is simple, but it can be a source of frustration for newbies. In this article we introduce the RANKX function with a few examples. [» Read more](https://www.sqlbi.com/articles/introducing-rankx-in-dax/)
- [**Introducing ALLSELECTED in DAX**](https://www.sqlbi.com/articles/introducing-allselected-in-dax/)

  ALLSELECTED is an extremely complex function to use in DAX. In this article, we provide an introduction to ALLSELECTED and its main use cases, leaving the most intricate details to more advanced articles. [» Read more](https://www.sqlbi.com/articles/introducing-allselected-in-dax/)
- [**Using ALLSELECTED in composite models**](https://www.sqlbi.com/articles/using-allselected-in-composite-models/)

  Using ALLSELECTED with no arguments in a remote model later used in a composite model might produce unexpected results. In this article we examine the topic and provide the reasons why ALLSELECTED requires special attention. [» Read more](https://www.sqlbi.com/articles/using-allselected-in-composite-models/)
- [**ALLSELECTED best practices**](https://www.sqlbi.com/articles/allselected-best-practices/)

  ALLSELECTED is a powerful, yet dangerous function. This article describes the best practices to follow to avoid falling into the pitfalls involved with ALLSELECTED. [» Read more](https://www.sqlbi.com/articles/allselected-best-practices/)
- [**ALL vs ALLSELECTED vs ALLEXCEPT vs REMOVEFILTERS**](https://www.sqlbi.com/articles/all-vs-allselected-vs-allexcept-vs-removefilters/)

  DAX offers many functions to remove filters from the filter context. In this article, we analyze the differences among the most commonly-used functions. [» Read more](https://www.sqlbi.com/articles/all-vs-allselected-vs-allexcept-vs-removefilters/)

## Related functions

Other related functions are:

- [[ALL]]
- [[ALLEXCEPT]]
- [[ALLNOBLANKROW]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, John Mayer, Pär Adeen, Mark Logan

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/allselected-function-dax](https://docs.microsoft.com/en-us/dax/allselected-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
