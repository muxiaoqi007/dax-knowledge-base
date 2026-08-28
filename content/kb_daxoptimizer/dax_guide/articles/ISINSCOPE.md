---
title: "ISINSCOPE"
function: "isinscope"
category: "Information"
url: "https://dax.guide/isinscope/"
source: "dax.guide"
重要度:
难度:
---

# ISINSCOPE DAX Function (Information)

Returns true when the specified column is the level in a hierarchy of levels.

## Syntax

ISINSCOPE ( <ColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName |  | The column of the level. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

Returns TRUE if the column is in included in the filter context and it is a grouping column for the current row in the result set.

## Remarks

ISINSCOPE checks whether there is a filter placed on the column derived either from context transition or from a group by column placed by [[SUMMARIZECOLUMNS]], which implies that there are only zero or one value in the current filter context.

When ISINSCOPE returns TRUE, [[HASONEVALUE]] might still return FALSE if there are no data for the intersection considered. This could happen in [[SUMMARIZECOLUMNS]], as described in the [Distinguishing HASONEVALUE from ISINSCOPE](https://www.sqlbi.com/articles/distinguishing-hasonevalue-from-isinscope) article.

[» 8 related articles](#articles)  
[» 4 related functions](#alt)  

## Examples

```dax


--  ISINSCOPE is useful to detect if a column is currently

--  in the rows or columns of a visual (like groupby columns 

--  of SUMMARIZECOLUMNS) AND it has only one value visible

EVALUATE

TOPN (

   20,

    SUMMARIZECOLUMNS (

        'Product'[Brand],

        Customer[CountryRegion],

        "Brand in scope", ISINSCOPE ( 'Product'[Brand] ),

        "Country in scope", ISINSCOPE ( Customer[CountryRegion] ),

        "Product name in scope", ISINSCOPE ( 'Product'[Product Name] )

    )

)



```

| Brand | CountryRegion | Brand in scope | Country in scope | Product name in scope |
| --- | --- | --- | --- | --- |
| Contoso | Australia | true | true | false |
| Wide World Importers | Australia | true | true | false |
| Northwind Traders | Australia | true | true | false |
| Adventure Works | Australia | true | true | false |
| Southridge Video | Australia | true | true | false |
| Litware | Australia | true | true | false |
| Fabrikam | Australia | true | true | false |
| Proseware | Australia | true | true | false |
| A. Datum | Australia | true | true | false |
| The Phone Company | Australia | true | true | false |
| Tailspin Toys | Australia | true | true | false |
| Contoso | United States | true | true | false |
| Wide World Importers | United States | true | true | false |
| Northwind Traders | United States | true | true | false |
| Adventure Works | United States | true | true | false |
| Southridge Video | United States | true | true | false |
| Litware | United States | true | true | false |
| Fabrikam | United States | true | true | false |
| Proseware | United States | true | true | false |
| A. Datum | United States | true | true | false |

```dax


--  In this example we use ISINSCOPE to compute the percentage

--  over the parent level in a hierarchy over products

DEFINE

    MEASURE Sales[Pct over parent] =

        VAR AllSales =

            CALCULATE ( [Sales Amount], ALLSELECTED () )

        VAR CategorySales =

            CALCULATE ( [Sales Amount], ALLSELECTED (), VALUES ( Product[Category] ) )

        VAR CurrentSales = [Sales Amount]

        RETURN

            SWITCH (

                TRUE (),

                ISINSCOPE ( 'Product'[Subcategory] ), DIVIDE ( CurrentSales, CategorySales ),

                ISINSCOPE ( 'Product'[Category] ),    DIVIDE ( CurrentSales, AllSales )

            )

EVALUATE

CALCULATETABLE (

    SUMMARIZECOLUMNS (

        'Product'[Category],

        ROLLUPADDISSUBTOTAL ( 'Product'[Subcategory], "Category total" ),

        "Sales Amount", [Sales Amount],

        "Pct over parent", [Pct over parent]

    ),

    'Product'[Category] IN { "Audio", "TV and Video" }

)

ORDER BY

    Product[Category],

    [Category Total],

    Product[Subcategory]

```

| Category | Subcategory | Category total | Sales Amount | Pct over parent |
| --- | --- | --- | --- | --- |
| Audio | Bluetooth Headphones | false | 124,450.79 | 32.37% |
| Audio | MP4&MP3 | false | 170,194.00 | 44.26% |
| Audio | Recording Pen | false | 89,873.37 | 23.37% |
| Audio | (Blank) | true | 384,518.16 | 8.05% |
| TV and Video | Car Video | false | 604,413.71 | 13.76% |
| TV and Video | Home Theater System | false | 1,525,526.26 | 34.73% |
| TV and Video | Televisions | false | 1,834,257.05 | 41.76% |
| TV and Video | VCD & DVD | false | 428,571.27 | 9.76% |
| TV and Video | (Blank) | true | 4,392,768.29 | 91.95% |

```dax


--  ISINSCOPE, like ALLSELECTED, deduces the information about

--  the grouping of a column from a different algorithm:

--  the column must show only one value in the filter context

--  and the filter must be coming from either a groupby column 

--  introduced by SUMMARIZECOLUMNS, or from a context transition.

--  Besides, the filter must be still active (not overridden)

EVALUATE

ADDCOLUMNS (

    VALUES ( 'Product'[Color] ),

    "ISINSCOPE 1",

	    CALCULATE ( 

	        INT ( ISINSCOPE ( 'Product'[Color] ) ),

	        FILTER ( VALUES ( Product[Color] ), TRUE )

	    ),

    "ISINSCOPE 2",

	    CALCULATE ( 

	        INT ( ISINSCOPE ( 'Product'[Color] ) ),

	        KEEPFILTERS ( FILTER ( VALUES ( Product[Color] ), TRUE ) )

	    )

)

```

| Color | ISINSCOPE 1 | ISINSCOPE 2 |
| --- | --- | --- |
| Silver | 0 | 1 |
| Blue | 0 | 1 |
| White | 0 | 1 |
| Red | 0 | 1 |
| Black | 0 | 1 |
| Green | 0 | 1 |
| Orange | 0 | 1 |
| Pink | 0 | 1 |
| Yellow | 0 | 1 |
| Purple | 0 | 1 |
| Brown | 0 | 1 |
| Grey | 0 | 1 |
| Gold | 0 | 1 |
| Azure | 0 | 1 |
| Silver Grey | 0 | 1 |
| Transparent | 0 | 1 |

## Related articles

Learn more about ISINSCOPE in the following articles:

- [**Filtering the Top 3 products for each category in Power BI**](https://www.sqlbi.com/articles/filtering-the-top-3-products-for-each-category-in-power-bi/)

  This article describes different techniques to display the first three products for each category in Power BI. It includes considerations on how to adapt the technique to different models and requirements. [» Read more](https://www.sqlbi.com/articles/filtering-the-top-3-products-for-each-category-in-power-bi/)
- [**Showing the top 5 products and Other row**](https://www.sqlbi.com/articles/showing-the-top-5-products-and-others-row/)

  This article shows how to add an additional “other” row to the selection obtained using the Top N filter in a Power BI report. [» Read more](https://www.sqlbi.com/articles/showing-the-top-5-products-and-others-row/)
- [**Distinguishing HASONEVALUE from ISINSCOPE**](https://www.sqlbi.com/articles/distinguishing-hasonevalue-from-isinscope/)

  This article describes the differences between HASONEVALUE and ISINSCOPE, which are two useful DAX functions to control the filters and the grouping that are active in a report. [» Read more](https://www.sqlbi.com/articles/distinguishing-hasonevalue-from-isinscope/)
- [**Introducing the RANK window function in DAX**](https://www.sqlbi.com/articles/introducing-the-rank-window-function-in-dax/)

  RANK is a new DAX function to rank items based on multiple columns. This article introduces the RANK function and its differences with RANKX. [» Read more](https://www.sqlbi.com/articles/introducing-the-rank-window-function-in-dax/)
- [**Using RANK instead of RANKX in DAX**](https://www.sqlbi.com/articles/using-rank-instead-of-rankx-in-dax/)

  Should you use RANK or stick with RANKX? In which scenarios is one better than the other? This article provides an in-depth analysis to help readers make informed choices. [» Read more](https://www.sqlbi.com/articles/using-rank-instead-of-rankx-in-dax/)
- [**Measuring the impact of promotions on sales in Power BI**](https://www.sqlbi.com/articles/measuring-the-impact-of-promotions-on-sales-in-power-bi/)

  This article describes the data model and DAX measures to analyze the effectiveness of campaigns, by separating attributed sales (directly linked to a campaign) from influenced sales (all sales of products participating in campaigns, regardless of attribution). [» Read more](https://www.sqlbi.com/articles/measuring-the-impact-of-promotions-on-sales-in-power-bi/)
- [**Show transaction details on the matrix visual in Power BI**](https://www.sqlbi.com/articles/show-transaction-details-on-the-matrix-visual-in-power-bi/)

  This article shows how to create a DAX measure that displays information from multiple columns in a business entity or transaction, into a single column of a matrix. [» Read more](https://www.sqlbi.com/articles/show-transaction-details-on-the-matrix-visual-in-power-bi/)
- [**Dynamic formatting by hierarchy level with ISINSCOPE and ISATLEVEL**](https://www.sqlbi.com/articles/dynamic-formatting-by-hierarchy-level-with-isinscope-and-isatlevel/)

  This article describes how to apply different formatting rules at each level of a hierarchy (one rule at the year level, another at the quarter level, another at the month level) using ISINSCOPE in a measure or ISATLEVEL in a visual calculation. [» Read more](https://www.sqlbi.com/articles/dynamic-formatting-by-hierarchy-level-with-isinscope-and-isatlevel/)

## Related functions

Other related functions are:

- [[ISCROSSFILTERED]]
- [[ISFILTERED]]
- [[ALLSELECTED]]
- [[HASONEVALUE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber, Alexander Horn

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/isinscope-function-dax](https://docs.microsoft.com/en-us/dax/isinscope-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
