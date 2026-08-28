---
title: "EXCEPT"
function: "except"
category: "Table manipulation"
url: "https://dax.guide/except/"
source: "dax.guide"
重要度:
难度:
---

# EXCEPT DAX Function (Table manipulation)

Returns the rows of left-side table which do not appear in right-side table.

## Syntax

EXCEPT ( <LeftTable>, <RightTable> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| LeftTable |  | The Left-side table expression to be used for Except. |
| RightTable |  | The Right-side table expression to be used for Except. |

## Return values

Table An entire table or a table with one or more columns.

A table that contains the rows of the LeftTable minus all the rows of the RightTable.

## Remarks

If a row appears at all in both tables, it and its duplicates are not present in the result set. If a row appears in only LeftTable, it and its duplicates will appear in the result set.

The column names will match the column names in LeftTable.

The returned table has [lineage](https://www.sqlbi.com/articles/understanding-data-lineage-in-dax/) based on the columns in LeftTable, regardless of the lineage of the columns in the second table. For example, if the first column of first table\_expression has lineage to the base column C1 in the model, the Except will reduce the rows based on the availability of values in the first column of second table\_expression and keep the lineage on base column C1 intact.

The two tables must have the same number of columns.

Columns are compared based on positioning, and data comparison with no type coercion.

The set of rows returned depends on the order of the two expressions.

The returned table does not include columns from tables related to LeftTable. Therefore, when LeftTable corresponds to a base table, once applied to the filter context it does not involve the expanded table and it only filters columns of he base table.

The returned table is never an expanded table when applied to the filter context, it only includes the base table.

[» 5 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  EXCEPT performs set subtraction: the second parameter rows are removed

--  from the first.

EVALUATE 

VAR Days        = VALUES ( 'Date'[Day of Week] )

VAR WeekendDays = { "Saturday", "Sunday" }

VAR WorkingDays = EXCEPT ( Days, WeekendDays )

RETURN 

    WorkingDays

```

| Day of Week |
| --- |
| Monday |
| Tuesday |
| Wednesday |
| Thursday |
| Friday |

```dax


--  EXCEPT keeps the data lineage of its first argument only.

EVALUATE 

VAR Days        = VALUES ( 'Date'[Day of Week] )

VAR WeekendDays = { "Saturday", "Sunday" }

VAR WorkingDays = EXCEPT ( Days, WeekendDays )

RETURN 

    ADDCOLUMNS ( 

        WorkingDays,

        "Sales Amount", [Sales Amount] 

    )

```

| Day of Week | Sales Amount |
| --- | --- |
| Monday | 4,251,342.00 |
| Tuesday | 4,643,891.92 |
| Wednesday | 4,021,583.39 |
| Thursday | 4,653,030.30 |
| Friday | 4,433,004.10 |

```dax


--  EXCEPT keeps duplicates, if present in the first parameter.

EVALUATE 

VAR Days        = SELECTCOLUMNS ( 'Date', "Day of week", 'Date'[Day of Week] )

VAR WeekendDays = { "Saturday", "Sunday" }

VAR WorkingDays = EXCEPT ( Days, WeekendDays )

RETURN 

    ADDCOLUMNS ( 

        TOPN ( 10, WorkingDays ),

        "Sales Amount", [Sales Amount] 

    )

```

| Day of week | Sales Amount |
| --- | --- |
| Friday | 4,433,004.10 |
| Monday | 4,251,342.00 |
| Tuesday | 4,643,891.92 |
| Wednesday | 4,021,583.39 |
| Thursday | 4,653,030.30 |
| Friday | 4,433,004.10 |
| Monday | 4,251,342.00 |
| Tuesday | 4,643,891.92 |
| Wednesday | 4,021,583.39 |
| Thursday | 4,653,030.30 |

The arguments of EXCEPT must have the same number of columns. The following query throws an error because Date contains many more columns than WeekendDays.

```dax


EVALUATE 

VAR Days        = 'Date'

VAR WeekendDays = { "Saturday", "Sunday" }

VAR WorkingDays = EXCEPT ( Days, WeekendDays )

RETURN 

    ADDCOLUMNS ( 

        WorkingDays,

        "Sales Amount", [Sales Amount] 

    )

```

## Related articles

Learn more about EXCEPT in the following articles:

- [**Computing New Customers in DAX**](https://www.sqlbi.com/articles/computing-new-customers-in-dax/)

  In this article, Alberto Ferrari describes a new efficient way to compute returning customers in DAX thanks to an idea suggested by a student attending an Optimizing DAX workshop. [» Read more](https://www.sqlbi.com/articles/computing-new-customers-in-dax/)
- [**Finding products without sales by using DAX**](https://www.sqlbi.com/articles/finding-products-without-sales-by-using-dax/)

  This article analyzes the performance of different DAX techniques to identify any products without sales in an area or a time period. [» Read more](https://www.sqlbi.com/articles/finding-products-without-sales-by-using-dax/)
- [**Using CONTAINS in DAX**](https://www.sqlbi.com/articles/using-contains-in-dax/)

  This article explains how the CONTAINS function works and what can be used as better alternatives in DAX in common use cases. [» Read more](https://www.sqlbi.com/articles/using-contains-in-dax/)
- [**Set functions in DAX: UNION, INTERSECT, and EXCEPT**](https://www.sqlbi.com/articles/set-functions-in-dax-union-intersect-and-except/)

  This article describes the behavior of the DAX functions that manipulate sets; they are useful to create queries and sometimes also to author measures. [» Read more](https://www.sqlbi.com/articles/set-functions-in-dax-union-intersect-and-except/)
- [**Creating functions for the like-for-like DAX pattern**](https://www.sqlbi.com/articles/creating-functions-for-the-like-for-like-dax-pattern/)

  This article offers a comprehensive guide to changing the like-for-like pattern into model-independent functions to enhance flexibility and simplify DAX code. [» Read more](https://www.sqlbi.com/articles/creating-functions-for-the-like-for-like-dax-pattern/)

## Related functions

Other related functions are:

- [[INTERSECT]]
- [[UNION]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/except-function-dax](https://docs.microsoft.com/en-us/dax/except-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
