---
title: "UNION"
function: "union"
category: "Table manipulation"
url: "https://dax.guide/union/"
source: "dax.guide"
重要度:
难度:
---

# UNION DAX Function (Table manipulation)

Returns the union of the tables whose columns match.

## Syntax

UNION ( <Table>, <Table> [, <Table> [, … ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table | Repeatable | A table that will participate in the union. |

## Return values

Table An entire table or a table with one or more columns.

A table that contains all the rows from each of the table expressions.

## Remarks

The tables must have the same number of columns.

Columns are combined by position in their respective tables.

The column names in the return table will match the column names in the first Table.

Duplicate rows are retained.

The returned table has [lineage](https://www.sqlbi.com/articles/understanding-data-lineage-in-dax/) where possible. For example, if the first column of each Table has lineage to the same base column C1 in the model, the first column in the UNION result will have lineage to C1. However, if combined columns have lineage to different base columns, or if there is an extension column, the resulting column in UNION will have no lineage.

When data types differ, the resulting data type is determined based on the rules for data type coercion.

The returned table will not contain columns from related tables. Therefore, when the result corresponds to a base table, once applied to the filter context it does not involve the expanded table and it only filters columns of the base table.

[» 6 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  UNION performs set addition: the second parameter rows 

--  are added to the first, keeping duplicates

EVALUATE 

VAR Days        = VALUES ( 'Date'[Day of Week] )

VAR WeekendDays = { "Saturday", "Sunday" }

VAR UnionDays   = UNION ( Days, WeekendDays )

RETURN 

    UnionDays

```

| Day of Week |
| --- |
| Saturday |
| Sunday |
| Monday |
| Tuesday |
| Wednesday |
| Thursday |
| Friday |
| Saturday |
| Sunday |

```dax


--  UNION keeps the data lineage only if all its arguments share

--  the same data lineage

DEFINE

VAR Days        = VALUES ( 'Date'[Day of Week] )

VAR WeekendDays = { "Saturday", "Sunday" }

VAR UnionDays   = UNION ( Days, WeekendDays )



EVALUATE

    ADDCOLUMNS ( 

        Days,

        "Sales Amount", [Sales Amount] 

    )

    

EVALUATE

    ADDCOLUMNS ( 

        UnionDays,

        "Sales Amount", [Sales Amount] 

    )

```

| Day of Week | Sales Amount |
| --- | --- |
| Saturday | 4,332,879.26 |
| Sunday | 4,255,613.01 |
| Monday | 4,251,342.00 |
| Tuesday | 4,643,891.92 |
| Wednesday | 4,021,583.39 |
| Thursday | 4,653,030.30 |
| Friday | 4,433,004.10 |

| Day of Week | Sales Amount |
| --- | --- |
| Saturday | 30,591,343.98 |
| Sunday | 30,591,343.98 |
| Monday | 30,591,343.98 |
| Tuesday | 30,591,343.98 |
| Wednesday | 30,591,343.98 |
| Thursday | 30,591,343.98 |
| Friday | 30,591,343.98 |
| Saturday | 30,591,343.98 |
| Sunday | 30,591,343.98 |

The arguments of UNION must have the same number of columns.  
The following query throws an error: *Date* contains more columns than *WeekendDays*.

```dax


EVALUATE 

VAR Days        = Date

VAR WeekendDays = { "Saturday", "Sunday" }

VAR UnionDays   = UNION ( Days, WeekendDays )

RETURN 

    ADDCOLUMNS ( 

        UnionDays,

        "Sales Amount", [Sales Amount] 

    )

```

## Related articles

Learn more about UNION in the following articles:

- [**Transition Matrix Using Calculated Tables**](https://www.sqlbi.com/articles/transition-matrix-using-calculated-tables/)

  In the 2015 September update, Power BI introduced calculated tables, which are computed using DAX expressions instead of being loaded from a data source. This article shows the usage of calculated tables to solve the pattern of transition matrix for… [» Read more](https://www.sqlbi.com/articles/transition-matrix-using-calculated-tables/)
- [**Creating a slicer that filters multiple columns in Power BI**](https://www.sqlbi.com/articles/creating-a-slicer-that-filters-multiple-columns-in-power-bi/)

  This article describes how to create a slicer showing the values of multiple columns, applying the filter on any of the underlying columns. [» Read more](https://www.sqlbi.com/articles/creating-a-slicer-that-filters-multiple-columns-in-power-bi/)
- [**Showing the top 5 products and Other row**](https://www.sqlbi.com/articles/showing-the-top-5-products-and-others-row/)

  This article shows how to add an additional “other” row to the selection obtained using the Top N filter in a Power BI report. [» Read more](https://www.sqlbi.com/articles/showing-the-top-5-products-and-others-row/)
- [**Preparing a data model for Sankey Charts in Power BI**](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)

  This article describes how to correctly shape a data model and prepare data to use a Sankey Chart as a funnel, considering events related to a customer (contact, trial, subscription, renewal, and others). [» Read more](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)
- [**Set functions in DAX: UNION, INTERSECT, and EXCEPT**](https://www.sqlbi.com/articles/set-functions-in-dax-union-intersect-and-except/)

  This article describes the behavior of the DAX functions that manipulate sets; they are useful to create queries and sometimes also to author measures. [» Read more](https://www.sqlbi.com/articles/set-functions-in-dax-union-intersect-and-except/)
- [**Account receivable aging in Power BI**](https://www.sqlbi.com/articles/account-receivable-aging-in-power-bi/)

  This article describes an Accounts Receivable Aging report in Power BI, and shows how to simplify a business problem using existing modeling patterns. [» Read more](https://www.sqlbi.com/articles/account-receivable-aging-in-power-bi/)

## Related functions

Other related functions are:

- [[EXCEPT]]
- [[INTERSECT]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/union-function-dax](https://docs.microsoft.com/en-us/dax/union-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
