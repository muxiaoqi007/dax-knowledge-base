---
title: "SUMMARIZECOLUMNS"
function: "summarizecolumns"
category: "Table manipulation"
url: "https://dax.guide/summarizecolumns/"
source: "dax.guide"
重要度:
难度:
---

# SUMMARIZECOLUMNS DAX Function (Table manipulation)

Create a summary table for the requested totals over set of groups.

## Syntax

SUMMARIZECOLUMNS ( [<GroupBy\_ColumnName> [, [<FilterTable>] [, [<Name>] [, [<Expression>] [, <GroupBy\_ColumnName> [, [<FilterTable>] [, [<Name>] [, [<Expression>] [, … ] ] ] ] ] ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| GroupBy\_ColumnName | Optional Repeatable | A column to group by or a call to [[ROLLUPGROUP]] function and [[ROLLUPADDISSUBTOTAL]] function to specify a list of columns to group by with subtotals. |
| FilterTable | Optional Repeatable | An expression that defines the table from which rows are to be returned, or a modifier like [[KEEPFILTERS]] or [[REMOVEFILTERS]].  The expression can be embedded in a [[NONVISUAL]] function, which marks a value filter in SUMMARIZECOLUMNS function as not affecting measure values, but only applying to group-by columns.  The expression can be embedded in a [[KEEPFILTERS]] modifier.  The expression can be a [[REMOVEFILTERS]] function with any argument, including no arguments. |
| Name | Optional Repeatable | A column name to be added. |
| Expression  By Expression | Optional Repeatable | The expression of the new column. |

## Return values

Table An entire table or a table with one or more columns.

A table which includes combinations of values from the supplied columns, based on the grouping specified. Only rows for which at least one of the supplied expressions return a non-blank value are included in the table returned. If all expressions evaluate to [[BLANK]] for a row, that row is not included in the table returned.

## Remarks

SUMMARIZECOLUMNS can be used in measures with DAX engines released from June 2024.

Until February 2023, SUMMARIZECOLUMNS did not support evaluation within a context transition at all. In products released before that month, this limitation made SUMMARIZECOLUMNS not useful in most of the measures – it was not possible to call a measure SUMMARIZECOLUMNS in any case of context transition, including other SUMMARIZECOLUMNS statements. Client tools like Excel and Power BI almost always generate context transitions to evaluate measures in the reports. From February 2023, the context transition was supported in a few scenario, but not in all the conditions.  
Since June 2024, SUMMARIZECOLUMNS should support any context transition and external filters, with a few remaining limitations described in Microsoft documentation.

SUMMARIZECOLUMNS always combines all the filters on the same table into a single filter. The combined table resulting from this filter only contains columns explicitly listed in SUMMARIZECOLUMNS as grouping columns or filter columns. This is the [auto-exists](https://www.sqlbi.com/articles/understanding-dax-auto-exist/) behavior that has side effects on functions such as [[FILTERS]].

Filters in SUMMARIZECOLUMNS only apply to group-by columns from the same table and to measures. They do not apply to group-by columns from other tables directly, but indirectly through the implied non-empty filter from measures. In order to apply a filter to the group-by column unconditionally, apply the filter through a [[CALCULATETABLE]] function that evaluates SUMMARIZECOLUMNS.

The values obtained for the group-by columns include the blank values of invalid relationships.  
For example, the following SUMMARIZECOLUMNS function:

```dax


SUMMARIZECOLUMNS (

    'Date'[Calendar Year],

    'Product'[Color],

    "Result", [Sales Amount]

)

```

is like an optimized version of the following DAX expression:

```dax


FILTER (

    ADDCOLUMNS (

        CROSSJOIN (

            VALUES ( 'Date'[Calendar Year] ),

            VALUES ( 'Product'[Color] )

        ),

        "Result", [Sales Amount]

    ),

    NOT ( ISBLANK ( [Result] ) )

)

```

SUMMARIZECOLUMNS does not preserve the [data lineage](https://www.sqlbi.com/articles/understanding-data-lineage-in-dax/) of the columns used in [[ROLLUPADDISSUBTOTAL]] or [[ROLLUPGROUP]]. If such columns are later used in the filter context, they are ignored. This behavior is different from [[SUMMARIZE]], where the same situation raises an error.

[» 12 related articles](#articles)  
[» 7 related functions](#alt)  

## Examples

```dax


--  SUMMARIZECOLUMNS is the primary querying function in DAX

--  It provides most querying features in a single function:

--      First set of arguments are the groupby columns

--      Second set are the filters

--      Third set are additional columns added to the resultset

EVALUATE

SUMMARIZECOLUMNS ( 

    'Product'[Brand],

    'Date'[Calendar Year],

    TREATAS ( { "CY 2008", "CY 2009" }, 'Date'[Calendar Year] ),

    TREATAS ( { "Red", "Blue" }, 'Product'[Color] ),

    "Amount", [Sales Amount],

    "Qty", SUM ( Sales[Quantity] )

)

```

| Brand | Calendar Year | Amount | Qty |
| --- | --- | --- | --- |
| Contoso | CY 2008 | 449,704.15 | 1,851 |
| Wide World Importers | CY 2008 | 87,108.20 | 213 |
| Northwind Traders | CY 2008 | 152,389.53 | 263 |
| Adventure Works | CY 2008 | 74,735.98 | 230 |
| Southridge Video | CY 2008 | 9,902.09 | 688 |
| Litware | CY 2008 | 244,134.07 | 611 |
| Fabrikam | CY 2008 | 274,937.21 | 557 |
| Proseware | CY 2008 | 50,642.52 | 178 |
| A. Datum | CY 2008 | 27,733.20 | 60 |
| Tailspin Toys | CY 2008 | 22,856.00 | 677 |
| Contoso | CY 2009 | 298,096.75 | 1,703 |
| Wide World Importers | CY 2009 | 75,496.96 | 312 |
| Northwind Traders | CY 2009 | 73,747.19 | 156 |
| Adventure Works | CY 2009 | 81,845.29 | 216 |
| Southridge Video | CY 2009 | 10,696.16 | 692 |
| Litware | CY 2009 | 184,703.58 | 756 |
| Fabrikam | CY 2009 | 222,621.82 | 506 |
| Proseware | CY 2009 | 71,255.37 | 371 |
| Tailspin Toys | CY 2009 | 51,084.87 | 1,797 |

```dax


--  SUMMARIZECOLUMNS can compute subtotals as part of the query result

EVALUATE

SUMMARIZECOLUMNS (

    ROLLUPADDISSUBTOTAL ( 'Product'[Brand], "Brand total" ),

    ROLLUPADDISSUBTOTAL ( 'Date'[Calendar Year], "Year total" ),

    TREATAS ( { "CY 2008" }, 'Date'[Calendar Year] ),

    TREATAS ( { "Contoso", "Litware" }, 'Product'[Brand] ),

    "Amount", [Sales Amount],

    "Qty", SUM ( Sales[Quantity] )

)

ORDER BY [Year Total] ASC, [Calendar Year], [Brand Total] ASC, [Brand]

```

| Brand | Calendar Year | Brand total | Year total | Amount | Qty |
| --- | --- | --- | --- | --- | --- |
| Contoso | CY 2008 | false | false | 2,369,167.68 | 14,901 |
| Litware | CY 2008 | false | false | 1,487,846.74 | 3,364 |
| (Blank) | CY 2008 | true | false | 3,857,014.43 | 18,265 |
| Contoso | (Blank) | false | true | 2,369,167.68 | 14,901 |
| Litware | (Blank) | false | true | 1,487,846.74 | 3,364 |
| (Blank) | (Blank) | true | true | 3,857,014.43 | 18,265 |

```dax


--  Blank values are automatically removed from the output, 

--  unless you use the IGNORE modifier for newly introduced

--  columns.

--  Removed rows can also be reintroduced later by using ADDMISSINGITEMS

EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Calendar Year], 

    "Amount", [Sales Amount]

)



EVALUATE

ADDMISSINGITEMS ( 

    'Date'[Calendar Year], 

    SUMMARIZECOLUMNS (

        'Date'[Calendar Year], 

        "Amount", [Sales Amount]

    ),

    'Date'[Calendar Year]

)

ORDER BY 'Date'[Calendar Year]



```

| Calendar Year | Amount |
| --- | --- |
| CY 2007 | 11,309,946.12 |
| CY 2008 | 9,927,582.99 |
| CY 2009 | 9,353,814.87 |

| Calendar Year | Amount |
| --- | --- |
| CY 2005 | (Blank) |
| CY 2006 | (Blank) |
| CY 2007 | 11,309,946.12 |
| CY 2008 | 9,927,582.99 |
| CY 2009 | 9,353,814.87 |
| CY 2010 | (Blank) |
| CY 2011 | (Blank) |

```dax


-- This query returns 0 rows, because all the Sales transactions

-- have an Order Date that exists in Date

EVALUATE

CALCULATETABLE (

    VALUES ( Sales[Order Date] ),

    TREATAS ( { BLANK () }, 'Date'[Date] )

)

ORDER BY Sales[Order Date]



-- This query returns a list of all the unique values in Sales[Order Date]

-- because the filter applied to 'Date'[Date] has only effects on the Date table

-- The Sales table is unaffected by that filter.

EVALUATE

SUMMARIZECOLUMNS (

    Sales[Order Date],

    TREATAS ( { BLANK () }, 'Date'[Date] )

)

ORDER BY Sales[Order Date]



-- This query returns 0 rows, because COUNTROWS ( Sales ) 

-- always returns blank, being executed in a filter context 

-- that includes 'Date'[Date] and Sales[Order Date]

-- Because there are no rows in Sales with an invalid Order Date,

-- there are no rows with a non-blank result for COUNTROWS ( Sales )

EVALUATE

SUMMARIZECOLUMNS (

    Sales[Order Date],

    TREATAS ( { BLANK () }, 'Date'[Date] ),

    "# Transactions", COUNTROWS ( Sales )

)

ORDER BY Sales[Order Date]

```

## Related articles

Learn more about SUMMARIZECOLUMNS in the following articles:

- [**Introducing SUMMARIZECOLUMNS**](https://www.sqlbi.com/articles/introducing-summarizecolumns/)

  This article explains how to use SUMMARIZECOLUMNS, which is a replacement of SUMMARIZE and does not require the use of ADDCOLUMNS to obtain good performance. [» Read more](https://www.sqlbi.com/articles/introducing-summarizecolumns/)
- [**Understanding DAX Auto-Exist**](https://www.sqlbi.com/articles/understanding-dax-auto-exist/)

  This article describes the behavior of auto-exist in DAX, explaining the side effects of combining slicers on columns of the same table in Power BI. [» Read more](https://www.sqlbi.com/articles/understanding-dax-auto-exist/)
- [**Naming temporary columns in DAX**](https://www.sqlbi.com/articles/naming-temporary-columns-in-dax/)

  This article describes a naming convention for temporary columns in DAX expressions to avoid ambiguity with the measure reference notation. [» Read more](https://www.sqlbi.com/articles/naming-temporary-columns-in-dax/)
- [**Implementing real-time updates in Power BI using push datasets instead of DirectQuery**](https://www.sqlbi.com/articles/implementing-real-time-updates-in-power-bi-using-push-datasets-instead-of-directquery/)

  Push datasets are an efficient and inexpensive way to implement real-time updates in Power BI reports and dashboards. They can provide better performance and scalability than DirectQuery at the price of a small development cost. [» Read more](https://www.sqlbi.com/articles/implementing-real-time-updates-in-power-bi-using-push-datasets-instead-of-directquery/)
- [**Optimizing fusion optimization for DAX measures**](https://www.sqlbi.com/articles/optimizing-fusion-optimization-for-dax-measures/)

  This article describes how to implement a DAX measure to run faster than what you get from the built-in fusion optimization. [» Read more](https://www.sqlbi.com/articles/optimizing-fusion-optimization-for-dax-measures/)
- [**Introducing horizontal fusion in DAX**](https://www.sqlbi.com/articles/introducing-horizontal-fusion-in-dax/)

  Horizontal fusion is a new optimization technique available in DAX to reduce the number of storage engine queries. In this article, we introduce this optimization with some examples. [» Read more](https://www.sqlbi.com/articles/introducing-horizontal-fusion-in-dax/)
- [**Introducing wholesale and retail execution in composite models**](https://www.sqlbi.com/articles/introducing-wholesale-and-retail-execution-in-composite-models/)

  In composite models, any query can be executed on the remote model (wholesale execution) or by mixing local and remote engines together (retail execution). This article describes the differences between the wholesale and retail modes, along with examples. [» Read more](https://www.sqlbi.com/articles/introducing-wholesale-and-retail-execution-in-composite-models/)
- [**Creating real-time dashboards in Power BI with push datasets**](https://www.sqlbi.com/articles/creating-real-time-dashboards-in-power-bi-with-push-datasets/)

  Though you can build real-time reports with DirectQuery, push datasets offer a more scalable, economical, and effective solution especially when combined with an Import model already in place. In this article we introduce the architecture of push datasets. [» Read more](https://www.sqlbi.com/articles/creating-real-time-dashboards-in-power-bi-with-push-datasets/)
- [**ALLSELECTED best practices**](https://www.sqlbi.com/articles/allselected-best-practices/)

  ALLSELECTED is a powerful, yet dangerous function. This article describes the best practices to follow to avoid falling into the pitfalls involved with ALLSELECTED. [» Read more](https://www.sqlbi.com/articles/allselected-best-practices/)
- [**SQLBI+ updates in August 2025**](https://www.sqlbi.com/blog/marco/2025/08/05/sqlbi-updates-in-august-2025/)

  For many years, SUMMARIZECOLUMNS has been a function dedicated to DAX queries and calculated tables, but it was not supported in DAX measures. Over time, Microsoft lifted the limitations, and in June 2024, the function was declared as fully supported in measures. However, we never suggested widely adopting it because we wanted to study its behavior in detail, which was not necessary for the queries due to the limited side effects in that context. Time well spent, as we have now been able to document in detail how SUMMARIZECOLUMNS works, what to do, and what not to do. You will… [» Read more](https://www.sqlbi.com/blog/marco/2025/08/05/sqlbi-updates-in-august-2025/)
- [**SUMMARIZECOLUMNS best practices**](https://www.sqlbi.com/articles/summarizecolumns-best-practices/)

  SUMMARIZECOLUMNS is a powerful and complex function in DAX that in 2025 can be used in measures. This article outlines the best practices when using this function to avoid incorrect results. [» Read more](https://www.sqlbi.com/articles/summarizecolumns-best-practices/)
- [**Understanding value filter behavior in SUMMARIZECOLUMNS**](https://www.sqlbi.com/articles/understanding-value-filter-behavior-in-summarizecolumns/)

  Value filter behavior is a setting in Power BI semantic models that controls how filters are combined in SUMMARIZECOLUMNS. This article explains how it works and suggests its best configuration. [» Read more](https://www.sqlbi.com/articles/understanding-value-filter-behavior-in-summarizecolumns/)

## Related functions

Other related functions are:

- [[SUMMARIZE]]
- [[ADDMISSINGITEMS]]
- [[IGNORE]]
- [[NONVISUAL]]
- [[ROLLUPADDISSUBTOTAL]]
- [[ROLLUPGROUP]]
- [[ROLLUPISSUBTOTAL]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Bill Neil, Colin Maitland , Kenneth Barber, Daniil Maslyuk

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/summarizecolumns-function-dax](https://docs.microsoft.com/en-us/dax/summarizecolumns-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
