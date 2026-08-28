---
title: "SUMMARIZE"
function: "summarize"
category: "Table manipulation"
url: "https://dax.guide/summarize/"
source: "dax.guide"
重要度:
难度:
---

# SUMMARIZE DAX Function (Table manipulation)

Creates a summary of the input table grouped by the specified columns.

## Syntax

SUMMARIZE ( <Table> [, <GroupBy\_ColumnName> [, [<Name>] [, [<Expression>] [, <GroupBy\_ColumnName> [, [<Name>] [, [<Expression>] [, … ] ] ] ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table |  | The input table. |
| GroupBy\_ColumnName | Optional Repeatable | A column to group by or a call to [[ROLLUP]] function to specify a list of columns to group by with subtotals. |
| Name  Not recommended  Deprecated | Optional Repeatable | A column name to be added. |
| Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression  Not recommended  Deprecated | Optional Repeatable | The expression of the new column is executed in both a row context and a filter context. |

## Return values

Table An entire table or a table with one or more columns.

A table with the selected columns for the GroupBy\_ColumnName arguments and the summarized columns designed by the name arguments.

## Remarks

The GroupBy\_ColumnName must be either in table or in a related table to Table.

SUMMARIZE should not be used to add columns. As an alternative, use [[SUMMARIZECOLUMNS]] or [[ADDCOLUMNS]] / SUMMARIZE.

SUMMARIZE does not preserve the [data lineage](https://www.sqlbi.com/articles/understanding-data-lineage-in-dax/) of the columns used in [[ROLLUP]] or [[ROLLUPGROUP]], raising an error if such columns are later used in the filter context.

[» 8 related articles](#articles)  
[» 4 related functions](#alt)  

## Examples

```dax


--  SUMMARIZE groups its first argument by the set of columns 

--  provided in the next parameters.

--  The groupby columns can be any column of the expanded table.

EVALUATE

CALCULATETABLE (

    SUMMARIZE ( 

        Sales,

        'Product'[Brand],

        'Date'[Calendar Year]

    ),

    'Product'[Color] = "Silver Grey"

)



```

| Brand | Calendar Year |
| --- | --- |
| Contoso | 2007-01-01 |
| Fabrikam | 2007-01-01 |
| A. Datum | 2007-01-01 |
| Contoso | 2008-01-01 |
| Fabrikam | 2008-01-01 |
| A. Datum | 2008-01-01 |
| Contoso | 2009-01-01 |
| Fabrikam | 2009-01-01 |
| A. Datum | 2009-01-01 |

```dax


--  SUMMARIZE can also create new columns like ADDCOLUMNS does

--  even though we strongly discourage using this feature due

--  to the complexity of the result in some scenarios.

--  Columns are computed in both a row and a filter context

--  filtering the currently iterated row.

EVALUATE

CALCULATETABLE (

    SUMMARIZE ( 

        Sales,

        'Product'[Brand],

        'Date'[Calendar Year],

        "Qty", SUM ( Sales[Quantity] ),

        "Brand & Year", 'Product'[Brand] & " - " & 'Date'[Calendar Year]

    ),

    'Product'[Color] = "Silver Grey"

)

```

| Product[Brand] | Date[Calendar Year] | Qty | Brand & Year |
| --- | --- | --- | --- |
| Contoso | CY 2007 | 51 | Contoso – CY 2007 |
| Fabrikam | CY 2007 | 64 | Fabrikam – CY 2007 |
| A. Datum | CY 2007 | 173 | A. Datum – CY 2007 |
| Contoso | CY 2008 | 96 | Contoso – CY 2008 |
| Fabrikam | CY 2008 | 90 | Fabrikam – CY 2008 |
| A. Datum | CY 2008 | 149 | A. Datum – CY 2008 |
| Contoso | CY 2009 | 58 | Contoso – CY 2009 |
| Fabrikam | CY 2009 | 121 | Fabrikam – CY 2009 |
| A. Datum | CY 2009 | 157 | A. Datum – CY 2009 |

```dax


--  SUMMARIZE can produce subtotals using the ROLLUP

--  function to group columns.

EVALUATE

CALCULATETABLE (

    SUMMARIZE (

        Sales,

        ROLLUP ( 'Product'[Brand], 'Date'[Calendar Year] ),

        "Amt", [Sales Amount]

    ),

    'Product'[Brand] IN { "Contoso", "Litware" },

    'Date'[Calendar Year] IN { "CY 2008", "CY 2007" }

)

```

| Brand | Calendar Year | Amt |
| --- | --- | --- |
| Contoso | 2007-01-01 | 2,729,818.54 |
| Litware | 2007-01-01 | 647,385.82 |
| Contoso | 2008-01-01 | 2,369,167.68 |
| Litware | 2008-01-01 | 1,487,846.74 |
| Contoso | (Blank) | 5,098,986.22 |
| Litware | (Blank) | 2,135,232.56 |
| (Blank) | (Blank) | 7,234,218.78 |

```dax


--  SUMMARIZE has the option of grouping by columns computed

--  in the query.

EVALUATE

SUMMARIZE (

    ADDCOLUMNS (

        Sales,

        "SalesType",

            IF ( Sales[Quantity] > 1, "Large", "Small" )

    ),

    [SalesType],

    "Amt", [Sales Amount]

)

```

| SalesType | Amt |
| --- | --- |
| Small | 17,569,935.65 |
| Large | 13,021,408.33 |

## Related articles

Learn more about SUMMARIZE in the following articles:

- [**Best Practices Using SUMMARIZE and ADDCOLUMNS**](https://www.sqlbi.com/articles/best-practices-using-summarize-and-addcolumns/)

  Everyone using DAX is probably used to SQL query language. Because of the similarities between the Tabular data modeling and the relational data modeling, there is the expectation that you can perform the same operations as those allowed in SQL. However, in its current implementation DAX does not permit all the operations that you can […] [» Read more](https://www.sqlbi.com/articles/best-practices-using-summarize-and-addcolumns/)
- [**All the secrets of SUMMARIZE**](https://www.sqlbi.com/articles/all-the-secrets-of-summarize/)

  SUMMARIZE is a function that looks quite simple, but its functionality hides some secrets that might surprise even seasoned DAX coders. In this article, we analyze the behavior of SUMMARIZE, in order to completely describe its semantic. The final advice might surprise you: we will suggest to avoid the use of SUMMARIZE in your code, […] [» Read more](https://www.sqlbi.com/articles/all-the-secrets-of-summarize/)
- [**From SQL to DAX: Grouping Data**](https://www.sqlbi.com/articles/from-sql-to-dax-grouping-data/)

  The GROUP BY condition of a SQL statement is natively implemented by SUMMARIZE in DAX. This article shows how to use SUMMARIZE and an alternative syntax to group data. [» Read more](https://www.sqlbi.com/articles/from-sql-to-dax-grouping-data/)
- [**From SQL to DAX: Projection**](https://www.sqlbi.com/articles/from-sql-to-dax-projection/)

  This article describes projection functions and techniques in DAX, showing the differences between SELECTCOLUMNS, ADDCOLUMNS, and SUMMARIZE. [» Read more](https://www.sqlbi.com/articles/from-sql-to-dax-projection/)
- [**Using SELECTEDVALUE with Fields Parameters in Power BI**](https://www.sqlbi.com/blog/marco/2022/06/11/using-selectedvalue-with-fields-parameters-in-power-bi/)

  If you try to use SELECTEDVALUE on the visible column of the table generated by the Fields Parameters feature in Power BI, you get the following error: Calculation error in measure ‘Sales'[Selection]: Column [Parameter] is part of composite key, but… [» Read more](https://www.sqlbi.com/blog/marco/2022/06/11/using-selectedvalue-with-fields-parameters-in-power-bi/)
- [**Differences between GROUPBY and SUMMARIZE**](https://www.sqlbi.com/articles/differences-between-groupby-and-summarize/)

  Both GROUPBY and SUMMARIZE are useful functions to group by columns. However, they differ in both performance and functionalities. Knowing the details lets developers choose the right function for their specific scenario. [» Read more](https://www.sqlbi.com/articles/differences-between-groupby-and-summarize/)
- [**Understanding Group By Columns in Power BI**](https://www.sqlbi.com/articles/understanding-group-by-columns-in-power-bi/)

  This article describes how Power BI uses the Group By Columns attribute of a column and how you can leverage it in specific scenarios. [» Read more](https://www.sqlbi.com/articles/understanding-group-by-columns-in-power-bi/)
- [**Using VALUES in SUMMARIZE**](https://www.sqlbi.com/articles/using-values-in-summarize/)

  This article describes when to use VALUES in a table grouped by SUMMARIZE, then goes on to explain why you cannot however use VALUES with SUMMARIZECOLUMNS. [» Read more](https://www.sqlbi.com/articles/using-values-in-summarize/)

## Related functions

Other related functions are:

- [[SUMMARIZECOLUMNS]]
- [[ISSUBTOTAL]]
- [[ROLLUP]]
- [[ROLLUPGROUP]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Antonio Ladrón de Guevara Agudo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/summarize-function-dax](https://docs.microsoft.com/en-us/dax/summarize-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
