---
title: "LASTNONBLANKVALUE"
function: "lastnonblankvalue"
category: "Time Intelligence"
url: "https://dax.guide/lastnonblankvalue/"
source: "dax.guide"
重要度:
难度:
---

# LASTNONBLANKVALUE DAX Function (Time Intelligence) Context Transition

Returns the last non blank value of the expression that evaluated for the column.

## Syntax

LASTNONBLANKVALUE ( <ColumnName>, <Expression> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | The source values. || Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | The expression to be evaluated for each value. |

## Return values

Scalar A single value of any type.

The last non-blank value of Expression iterating ColumnName in natural ascending order.

## Notes

In order to use any time intelligence calculation, you need a well-formed date table. The *Date* table must satisfy the following requirements:

- All dates need to be present for the years required. The *Date* table must always start on January 1 and end on December 31, including all the days in this range. If the report only references fiscal years, then the date table must include all the dates from the first to the last day of a fiscal year. For example, if the fiscal year 2008 starts on July 1, 2007, then the *Date* table must include all the days from July 1, 2007 to June 30, 2008.
- There needs to be a column with a *DateTime* or *Date* data type containing unique values. This column is usually called *Date*. Even though the *Date* column is often used to define relationships with other tables, this is not required. Still, the *Date* column must contain unique values and should be referenced by the Mark as Date Table feature. In case the column also contains a time part, no time should be used – for example, the time should always be 12:00 am.
- The *Date* table must be marked as a date table in the model, in case the relationship between the *Date* table and any other table is not based on the *Date*.

The result of time intelligence functions has the same data lineage as the date column or table provided as an argument.

## Remarks

This function can cause **performance and memory issues**. Consider [alternative calculations in DAX](https://www.sqlbi.com/articles/optimizing-lastnonblank-and-lastnonblankvalue-calculations/).

The Expression is evaluated in a filter context obtained by a context transition. Therefore, even though the function iterates a column, it doesn’t provide a row context.

The ColumnName argument can be any of the following:

- A reference to a column. Only in this case a context transition applies because the column reference is replaced by
  - [[CALCULATETABLE]] ( [[DISTINCT]] ( <ColumnName> ) )
- A table expression that returns a single column.
- A Boolean expression that defines a single column.

Even though this function is commonly used for dates, it can be applied to a column of any data type.

The syntax:

```dax


LASTNONBLANKVALUE ( 

    <ColumnName>, 

    <Expression> 

)

```

corresponds to:

```dax


CALCULATE (

    <Expression>, 

    LASTNONBLANK ( 

        <ColumnName>, 

        CALCULATE ( <Expression> )

    )

)

```

[» 2 related articles](#articles)  
[» 3 related functions](#alt)  

## Examples

```dax


--  FIRSTNONBLANKVALUE and LASTNONBLANKVALUE return the first and last 

--  non blank values in the selection.

EVALUATE

CALCULATETABLE ( 

    ADDCOLUMNS ( VALUES ( 'Date'[Date] ), "Sales Amount", [Sales Amount] ),

    'Date'[Date] >= DATE ( 2007, 2, 8 ) &&

    'Date'[Date] <= DATE ( 2007, 2, 15 ) 

)



EVALUATE

CALCULATETABLE ( 

    { FIRSTNONBLANKVALUE ( 'Date'[Date], [Sales Amount] ) },

    'Date'[Date] >= DATE ( 2007, 2, 8 ) &&

    'Date'[Date] <= DATE ( 2007, 2, 15 ) 

)



EVALUATE

CALCULATETABLE ( 

    { LASTNONBLANKVALUE ( 'Date'[Date], [Sales Amount] ) },

    'Date'[Date] >= DATE ( 2007, 2, 8 ) &&

    'Date'[Date] <= DATE ( 2007, 2, 15 ) 

)



```

| Date | Sales Amount |
| --- | --- |
| 2007-02-08 | (Blank) |
| 2007-02-09 | 70,032.69 |
| 2007-02-10 | 27,487.70 |
| 2007-02-11 | 28,419.46 |
| 2007-02-12 | 64,176.29 |
| 2007-02-13 | 56,046.10 |
| 2007-02-14 | 26,612.37 |
| 2007-02-15 | (Blank) |

| Value |
| --- |
| 70,032.69 |

| Value |
| --- |
| 26,612.37 |

## Related articles

Learn more about LASTNONBLANKVALUE in the following articles:

- [**Semi-Additive Measures in DAX**](https://www.sqlbi.com/articles/semi-additive-measures-in-dax/)

  Values such as inventory and balance account, usually calculated from a snapshot table, require the use of semi-additive measures. In Multidimensional you have specific aggregation types, like LastChild and LastNonEmpty. In PowerPivot and Tabular you use DAX, which is flexible enough to implement any calculation, as described in this article. [» Read more](https://www.sqlbi.com/articles/semi-additive-measures-in-dax/)
- [**Optimizing LASTNONBLANK and LASTNONBLANKVALUE calculations**](https://www.sqlbi.com/articles/optimizing-lastnonblank-and-lastnonblankvalue-calculations/)

  This article explains the behavior of LASTNONBLANK, LASTNONBLANKVALUE, and similar DAX functions, also providing patterns for performance optimization. [» Read more](https://www.sqlbi.com/articles/optimizing-lastnonblank-and-lastnonblankvalue-calculations/)

## Related functions

Other related functions are:

- [[FIRSTNONBLANK]]
- [[FIRSTNONBLANKVALUE]]
- [[LASTNONBLANK]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/lastnonblankvalue-function-dax](https://docs.microsoft.com/en-us/dax/lastnonblankvalue-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
