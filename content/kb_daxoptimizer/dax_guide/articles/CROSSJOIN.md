---
title: "CROSSJOIN"
function: "crossjoin"
category: "Table manipulation"
url: "https://dax.guide/crossjoin/"
source: "dax.guide"
重要度:
难度:
---

# CROSSJOIN DAX Function (Table manipulation)

Returns a table that is a crossjoin of the specified tables.

## Syntax

CROSSJOIN ( <Table> [, <Table> [, … ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table | Repeatable | A table that will participate in the crossjoin. |

## Return values

Table An entire table or a table with one or more columns.

A table that contains the Cartesian product of all rows from all tables in the arguments.

## Remarks

Column names from table arguments must all be different in all tables or an error is returned.

The total number of rows returned by CROSSJOIN() is equal to the product of the number of rows from all tables in the arguments; also, the total number of columns in the result table is the sum of the number of columns in all tables. For example, if TableA has rA rows and cA columns, and TableB has rB rows and cB columns, and TableC has rC rows and cC column; then, the resulting table has rA × rB × rC rows and cA + cB + cC columns.

[» 5 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  CROSSJOIN performs the cartesian product of two tables, 

--  generating all the possible combinations of table rows

EVALUATE

CALCULATETABLE ( 

    ADDCOLUMNS (

        CROSSJOIN (

            VALUES ( 'Product'[Color] ),

            VALUES ( 'Product'[Category] )

        ),

        "#Prods", CALCULATE ( COUNTROWS ( 'Product' ) )

    ),

    'Product'[Category] IN { "TV and Video", "Computers" } 

)

ORDER BY [Category], [Color]



```

| Color | Category | #Prods |
| --- | --- | --- |
| Black | Computers | 194 |
| Blue | Computers | 20 |
| Brown | Computers | 11 |
| Gold | Computers | 4 |
| Green | Computers | 15 |
| Grey | Computers | 70 |
| Orange | Computers | 2 |
| Pink | Computers | 4 |
| Red | Computers | 15 |
| Silver | Computers | 78 |
| White | Computers | 188 |
| Yellow | Computers | 5 |
| Black | TV and Video | 72 |
| Blue | TV and Video | (Blank) |
| Brown | TV and Video | 53 |
| Gold | TV and Video | (Blank) |
| Green | TV and Video | (Blank) |
| Grey | TV and Video | (Blank) |
| Orange | TV and Video | (Blank) |
| Pink | TV and Video | (Blank) |
| Red | TV and Video | (Blank) |
| Silver | TV and Video | 71 |
| White | TV and Video | 26 |
| Yellow | TV and Video | (Blank) |

```dax


--  CROSSJOIN is useful to find non-existing combinations

--  that might be interesting to further analyze.

DEFINE

VAR Top5Colors =

    TOPN ( 5, VALUES ( 'Product'[Color] ), [Sales Amount] )

VAR CategoriesWithoutTop5Colors =

    FILTER (

        CROSSJOIN ( VALUES ( 'Product'[Category] ), Top5Colors ),

        CALCULATE ( ISEMPTY ( 'Product' ) )

    )



EVALUATE

    CategoriesWithoutTop5Colors

    

EVALUATE

    Top5Colors

```

| Category | Color |
| --- | --- |
| TV and Video | Blue |
| Cell phones | Blue |
| Audio | Grey |
| TV and Video | Grey |

| Color |
| --- |
| Blue |
| Grey |
| White |
| Silver |
| Black |

## Related articles

Learn more about CROSSJOIN in the following articles:

- [**From SQL to DAX: Joining Tables**](https://www.sqlbi.com/articles/from-sql-to-dax-joining-tables/)

  In SQL there are different types of JOIN, available for different purposes. This article shows the equivalent syntaxes supported in DAX and it was updated in May 2018. [» Read more](https://www.sqlbi.com/articles/from-sql-to-dax-joining-tables/)
- [**When to use KEEPFILTERS over iterators**](https://www.sqlbi.com/articles/when-to-use-keepfilters-over-iterators/)

  This article describes how to use KEEPFILTERS in DAX iterator functions to preserve arbitrarily shaped filters in context transition. [» Read more](https://www.sqlbi.com/articles/when-to-use-keepfilters-over-iterators/)
- [**Specifying multiple filter conditions in CALCULATE**](https://www.sqlbi.com/articles/specifying-multiple-filter-conditions-in-calculate/)

  This article introduces the new DAX syntax (March 2021) to support CALCULATE filter predicates that reference multiple columns from the same table. [» Read more](https://www.sqlbi.com/articles/specifying-multiple-filter-conditions-in-calculate/)
- [**Optimizing incremental inventory calculations in DAX**](https://www.sqlbi.com/articles/optimizing-incremental-inventory-calculations-in-dax/)

  This article describes how to optimize inventory calculations in DAX by using snapshots to avoid the computational cost of a complete running total. [» Read more](https://www.sqlbi.com/articles/optimizing-incremental-inventory-calculations-in-dax/)
- [**Understanding the “can’t determine relationship between the fields” error in Power BI**](https://www.sqlbi.com/articles/understanding-the-cant-determine-relationship-between-the-fields-error-in-power-bi/)

  This article explains why you might encounter a curious error when placing columns from unrelated tables in a Power BI matrix. [» Read more](https://www.sqlbi.com/articles/understanding-the-cant-determine-relationship-between-the-fields-error-in-power-bi/)

## Related functions

Other related functions are:

- [[EXCEPT]]
- [[INTERSECT]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/crossjoin-function-dax](https://docs.microsoft.com/en-us/dax/crossjoin-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
