---
title: "INTERSECT"
function: "intersect"
category: "Table manipulation"
url: "https://dax.guide/intersect/"
source: "dax.guide"
重要度:
难度:
---

# INTERSECT DAX Function (Table manipulation)

Returns the rows of left-side table which appear in right-side table.

## Syntax

INTERSECT ( <LeftTable>, <RightTable> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| LeftTable |  | The Left-side table expression to be used for Intersect. |
| RightTable |  | The Right-side table expression to be used for Intersect. |

## Return values

Table An entire table or a table with one or more columns.

A table that contains all the rows in LeftTable that are also in RightTable.

## Remarks

Intersect is not commutative. In general, Intersect(T1, T2) will have a different result set than Intersect(T2, T1).

Duplicate rows are retained. If a row appears in table\_expression1 and table\_expression2, it and all duplicates in table\_expression\_1 are included in the result set.

The column names will match the column names in table\_expression1.

The returned table has [lineage](https://www.sqlbi.com/articles/understanding-data-lineage-in-dax/) based on the columns in table\_expression1, regardless of the lineage of the columns in the second table. For example, if the first column of LeftTable has lineage to the base column C1 in the model, the intersect will reduce the rows based on the intersect on first column of RightTable and keep the lineage on base column C1 intact.

Columns are compared based on positioning, and data comparison does not have type coercion.

The returned table does not include columns from tables related to LeftTable. Therefore, when LeftTable corresponds to a base table, once applied to the filter context it does not involve the expanded table and it only filters columns of the base table.

[» 6 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  INTERSECT performs set intersection: the second parameter rows 

--  are intersected with the first.

--  INTERSECT keeps the data lineage of its first argument only.

DEFINE

VAR Days         = VALUES ( 'Date'[Day of Week] )

VAR WeekendDays  = { "Saturday", "Sunday" }

VAR DaysWeekends = INTERSECT ( Days, WeekendDays )

VAR WeekendsDays = INTERSECT ( WeekendDays, Days )



EVALUATE DaysWeekends



EVALUATE

ADDCOLUMNS ( 

    DaysWeekends,

    "Sales Amount", [Sales Amount] 

)



--  In this last result, the data lineage is from WeekendDays, which does not

--  filter the Sales table and the Sales Amount measure.

EVALUATE

ADDCOLUMNS ( 

    WeekendsDays,

    "Sales Amount", [Sales Amount] 

)

```

| Day of Week |
| --- |
| Saturday |
| Sunday |

| Day of Week | Sales Amount |
| --- | --- |
| Saturday | 4,332,879.26 |
| Sunday | 4,255,613.01 |

| Value | Sales Amount |
| --- | --- |
| Saturday | 30,591,343.98 |
| Sunday | 30,591,343.98 |

```dax


--  INTERSECT keeps duplicates, if present in the parameters.

--  In case of context transition, there are duplicated values 

--  in the results of the measures, too.

EVALUATE 

VAR Days        = SELECTCOLUMNS ( Date, "Day of week", 'Date'[Day of Week] )

VAR WeekendDays = { "Saturday", "Sunday" }

VAR Result      = INTERSECT ( Days, WeekendDays )

RETURN 

    ADDCOLUMNS ( 

        TOPN ( 5, Result ),

        "Sales Amount", [Sales Amount] 

    )

```

| Day of week | Sales Amount |
| --- | --- |
| Saturday | 4,332,879.26 |
| Sunday | 4,255,613.01 |
| Saturday | 4,332,879.26 |
| Sunday | 4,255,613.01 |
| Saturday | 4,332,879.26 |

The arguments of INTERSECT must have the same number of columns.  
The following query throws an error because Date contains many more columns than WeekendDays.

```dax


EVALUATE 

VAR Days        = Date

VAR WeekendDays = { "Saturday", "Sunday" }

VAR Result      = INTERSECT ( Days, WeekendDays )

RETURN 

    ADDCOLUMNS ( 

        Result,

        "Sales Amount", [Sales Amount] 

    )

```

## Related articles

Learn more about INTERSECT in the following articles:

- [**Leverage INTERSECT to apply relationships in DAX**](https://www.sqlbi.com/blog/marco/2016/07/26/leverage-intersect-to-apply-relationships-in-dax/)

  [» Read more](https://www.sqlbi.com/blog/marco/2016/07/26/leverage-intersect-to-apply-relationships-in-dax/)
- [**Physical and Virtual Relationships in DAX**](https://www.sqlbi.com/articles/physical-and-virtual-relationships-in-dax/)

  DAX calculations can leverage relationships present in the data model, but you can obtain the same result without physical relationships, applying equivalent filters using specific DAX patterns. This article show a more efficient technique to apply virtual relationships in DAX… [» Read more](https://www.sqlbi.com/articles/physical-and-virtual-relationships-in-dax/)
- [**Using tuple syntax in DAX expressions**](https://www.sqlbi.com/articles/using-tuple-syntax-in-dax-expressions/)

  This article describes the use of the tuple syntax in DAX expressions to simplify comparisons involving two or more columns. [» Read more](https://www.sqlbi.com/articles/using-tuple-syntax-in-dax-expressions/)
- [**Finding products without sales by using DAX**](https://www.sqlbi.com/articles/finding-products-without-sales-by-using-dax/)

  This article analyzes the performance of different DAX techniques to identify any products without sales in an area or a time period. [» Read more](https://www.sqlbi.com/articles/finding-products-without-sales-by-using-dax/)
- [**Using CONTAINS in DAX**](https://www.sqlbi.com/articles/using-contains-in-dax/)

  This article explains how the CONTAINS function works and what can be used as better alternatives in DAX in common use cases. [» Read more](https://www.sqlbi.com/articles/using-contains-in-dax/)
- [**Set functions in DAX: UNION, INTERSECT, and EXCEPT**](https://www.sqlbi.com/articles/set-functions-in-dax-union-intersect-and-except/)

  This article describes the behavior of the DAX functions that manipulate sets; they are useful to create queries and sometimes also to author measures. [» Read more](https://www.sqlbi.com/articles/set-functions-in-dax-union-intersect-and-except/)

## Related functions

Other related functions are:

- [[EXCEPT]]
- [[UNION]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/intersect-function-dax](https://docs.microsoft.com/en-us/dax/intersect-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
