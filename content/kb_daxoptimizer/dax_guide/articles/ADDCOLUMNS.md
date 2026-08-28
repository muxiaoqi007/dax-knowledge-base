---
title: "ADDCOLUMNS"
function: "addcolumns"
category: "Table manipulation"
url: "https://dax.guide/addcolumns/"
source: "dax.guide"
重要度:
难度:
---

# ADDCOLUMNS DAX Function (Table manipulation)

Returns a table with new columns specified by the DAX expressions.

## Syntax

ADDCOLUMNS ( <Table>, <Name>, <Expression> [, <Name>, <Expression> [, … ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | The table to which new columns are added. || Name | Repeatable | The name of the new column to be added. |
| Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression | Repeatable | The expression for the new column to be added. |

## Return values

Table An entire table or a table with one or more columns.

A table with all its original columns and the added ones.

## Remarks

ADDCOLUMNS does not preserve the [data lineage](https://www.sqlbi.com/articles/understanding-data-lineage-in-dax/) of the added columns for a following context transition, even if a column expression is a simple column reference. However, the data lineage of the column added is effective for [[NATURALLEFTOUTERJOIN]] and [[NATURALINNERJOIN]].

[» 8 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  ADDCOLUMNS is an iterator that returns its first argument

--  after adding the column specified.

--  New columns are computed in the row context of ADDCOLUMNS,

--  you need to invoke context transition to generate a filter

--  context, if needed.

EVALUATE

FILTER (

    ADDCOLUMNS (

        VALUES ( 'Date'[Calendar Year] ),

        "@Year Number",         INT ( RIGHT ( 'Date'[Calendar Year], 4 ) ),

        "@Amount",              [Sales Amount],

        "@Quantity Wrong",      SUM ( Sales[Quantity] ),

        "@Quantity Correct",    CALCULATE ( SUM ( Sales[Quantity] ) )

    ),

    [@Amount] > 0

)

```

| Calendar Year | @Year Number | @Amount | @Quantity Wrong | @Quantity Correct |
| --- | --- | --- | --- | --- |
| 2007-01-01 | 2007 | 11,309,946.12 | 140,180 | 44,310 |
| 2008-01-01 | 2008 | 9,927,582.99 | 140,180 | 40,226 |
| 2009-01-01 | 2009 | 9,353,814.87 | 140,180 | 55,644 |

## Related articles

Learn more about ADDCOLUMNS in the following articles:

- [**Best Practices Using SUMMARIZE and ADDCOLUMNS**](https://www.sqlbi.com/articles/best-practices-using-summarize-and-addcolumns/)

  Everyone using DAX is probably used to SQL query language. Because of the similarities between the Tabular data modeling and the relational data modeling, there is the expectation that you can perform the same operations as those allowed in SQL. However, in its current implementation DAX does not permit all the operations that you can […] [» Read more](https://www.sqlbi.com/articles/best-practices-using-summarize-and-addcolumns/)
- [**Using GENERATE and ROW instead of ADDCOLUMNS in DAX**](https://www.sqlbi.com/articles/using-generate-and-row-instead-of-addcolumns-in-dax/)

  This article explains how to improve DAX queries using GENERATE and ROW instead of ADDCOLUMNS when you create table expressions. [» Read more](https://www.sqlbi.com/articles/using-generate-and-row-instead-of-addcolumns-in-dax/)
- [**Highlighting the minimum and maximum values in a Power BI matrix**](https://www.sqlbi.com/articles/highlighting-the-minimum-and-maximum-values-in-a-power-bi-matrix/)

  This article shows how to use DAX and conditional formatting together to highlight the minimum and maximum values in a matrix in Power BI. [» Read more](https://www.sqlbi.com/articles/highlighting-the-minimum-and-maximum-values-in-a-power-bi-matrix/)
- [**From SQL to DAX: Grouping Data**](https://www.sqlbi.com/articles/from-sql-to-dax-grouping-data/)

  The GROUP BY condition of a SQL statement is natively implemented by SUMMARIZE in DAX. This article shows how to use SUMMARIZE and an alternative syntax to group data. [» Read more](https://www.sqlbi.com/articles/from-sql-to-dax-grouping-data/)
- [**Naming temporary columns in DAX**](https://www.sqlbi.com/articles/naming-temporary-columns-in-dax/)

  This article describes a naming convention for temporary columns in DAX expressions to avoid ambiguity with the measure reference notation. [» Read more](https://www.sqlbi.com/articles/naming-temporary-columns-in-dax/)
- [**From SQL to DAX: Projection**](https://www.sqlbi.com/articles/from-sql-to-dax-projection/)

  This article describes projection functions and techniques in DAX, showing the differences between SELECTCOLUMNS, ADDCOLUMNS, and SUMMARIZE. [» Read more](https://www.sqlbi.com/articles/from-sql-to-dax-projection/)
- [**Replacing relationships with join functions in DAX**](https://www.sqlbi.com/articles/replacing-relationships-with-join-functions-in-dax/)

  This article describes how to join tables in DAX when there are no relationships in the data model. The data lineage plays an essential role in this scenario. [» Read more](https://www.sqlbi.com/articles/replacing-relationships-with-join-functions-in-dax/)
- [**Computing accurate percentages with row-level security in Power BI**](https://www.sqlbi.com/articles/computing-accurate-percentages-with-row-level-security-in-power-bi/)

  This article shows how to compute ratios when row-level security hides some of the data. If the percentage also includes the hidden rows in the comparison, you should customize the data model and the measures involved to get the right result. [» Read more](https://www.sqlbi.com/articles/computing-accurate-percentages-with-row-level-security-in-power-bi/)

## Related functions

Other related functions are:

- [[SELECTCOLUMNS]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/addcolumns-function-dax](https://docs.microsoft.com/en-us/dax/addcolumns-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
