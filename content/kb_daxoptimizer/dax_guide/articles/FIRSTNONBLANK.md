---
title: "FIRSTNONBLANK"
function: "firstnonblank"
category: "Time Intelligence"
url: "https://dax.guide/firstnonblank/"
source: "dax.guide"
重要度:
难度:
---

# FIRSTNONBLANK DAX Function (Time Intelligence) Context Transition

Returns the first value in the column for which the expression has a non blank value.

## Syntax

FIRSTNONBLANK ( <ColumnName>, <Expression> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | The source values. || Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | The expression to be evaluated for each value. |

## Return values

Table A table with a single column.

A table containing a single column and single row with the computed first value.

## Notes

In order to use any time intelligence calculation, you need a well-formed date table. The *Date* table must satisfy the following requirements:

- All dates need to be present for the years required. The *Date* table must always start on January 1 and end on December 31, including all the days in this range. If the report only references fiscal years, then the date table must include all the dates from the first to the last day of a fiscal year. For example, if the fiscal year 2008 starts on July 1, 2007, then the *Date* table must include all the days from July 1, 2007 to June 30, 2008.
- There needs to be a column with a *DateTime* or *Date* data type containing unique values. This column is usually called *Date*. Even though the *Date* column is often used to define relationships with other tables, this is not required. Still, the *Date* column must contain unique values and should be referenced by the Mark as Date Table feature. In case the column also contains a time part, no time should be used – for example, the time should always be 12:00 am.
- The *Date* table must be marked as a date table in the model, in case the relationship between the *Date* table and any other table is not based on the *Date*.

The result of time intelligence functions has the same data lineage as the date column or table provided as an argument.

## Remarks

This function can cause **performance and memory issues**. Consider [alternative calculations in DAX](https://www.sqlbi.com/articles/optimizing-lastnonblank-and-lastnonblankvalue-calculations/).

The ColumnName argument can be any of the following:

- A reference to a column. Only in this case a context transition applies because the column reference is replaced by
  - [[CALCULATETABLE]] ( [[DISTINCT]] ( <ColumnName> ) )
- A table expression that returns a single column.
- A Boolean expression that defines a single-column.

The result table includes only values that exist in the ColumnName column.  
Even though this function is commonly used for dates, it can be applied to a column of any data type.

The ColumnName argument must be a column. In certain conditions the function does not return an error passing a table with more than one column as ColumnName argument, but the behavior in that case is not supported and the error condition is not reported because it could break existing reports.

[» 3 related articles](#articles)  
[» 5 related functions](#alt)  

## Examples

```dax


--  FIRSTNONBLANK returns the first date where the 

--  expression is not blank.

--  LASTNONBLANK returns the last date where the 

--  expression is not blank.

EVALUATE

CALCULATETABLE ( 

    ADDCOLUMNS ( VALUES ( 'Date'[Date] ), "Sales Amount", [Sales Amount] ),

    'Date'[Date] >= DATE ( 2007, 2, 8 ) &&

    'Date'[Date] <= DATE ( 2007, 2, 15 ) 

)

ORDER BY [Date]



EVALUATE

CALCULATETABLE ( 

    FIRSTNONBLANK ( 'Date'[Date], [Sales Amount] ),

    'Date'[Date] >= DATE ( 2007, 2, 8 ) &&

    'Date'[Date] <= DATE ( 2007, 2, 15 ) 

)



EVALUATE

CALCULATETABLE ( 

    LASTNONBLANK ( 'Date'[Date], [Sales Amount] ),

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

| Date |
| --- |
| 2007-02-09 |

| Date |
| --- |
| 2007-02-14 |

## Related articles

Learn more about FIRSTNONBLANK in the following articles:

- [**Alternative use of FIRSTNONBLANK and LASTNONBLANK**](https://www.sqlbi.com/articles/alternative-use-of-firstnonblank-and-lastnonblank/)

  You might have used FIRSTNONBLANK and LASTNONBLANK in semi-additive measures, but you might not be aware that their use is not limited to time intelligence functions. This article shows alternative scenarios where these functions are useful. [» Read more](https://www.sqlbi.com/articles/alternative-use-of-firstnonblank-and-lastnonblank/)
- [**Semi-Additive Measures in DAX**](https://www.sqlbi.com/articles/semi-additive-measures-in-dax/)

  Values such as inventory and balance account, usually calculated from a snapshot table, require the use of semi-additive measures. In Multidimensional you have specific aggregation types, like LastChild and LastNonEmpty. In PowerPivot and Tabular you use DAX, which is flexible enough to implement any calculation, as described in this article. [» Read more](https://www.sqlbi.com/articles/semi-additive-measures-in-dax/)
- [**Optimizing LASTNONBLANK and LASTNONBLANKVALUE calculations**](https://www.sqlbi.com/articles/optimizing-lastnonblank-and-lastnonblankvalue-calculations/)

  This article explains the behavior of LASTNONBLANK, LASTNONBLANKVALUE, and similar DAX functions, also providing patterns for performance optimization. [» Read more](https://www.sqlbi.com/articles/optimizing-lastnonblank-and-lastnonblankvalue-calculations/)

## Related functions

Other related functions are:

- [[FIRSTDATE]]
- [[LASTDATE]]
- [[LASTNONBLANK]]
- [[FIRSTNONBLANKVALUE]]
- [[LASTNONBLANKVALUE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/firstnonblank-function-dax](https://docs.microsoft.com/en-us/dax/firstnonblank-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
