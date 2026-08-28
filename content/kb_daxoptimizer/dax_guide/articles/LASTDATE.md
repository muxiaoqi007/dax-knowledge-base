---
title: "LASTDATE"
function: "lastdate"
category: "Time Intelligence"
url: "https://dax.guide/lastdate/"
source: "dax.guide"
重要度:
难度:
---

# LASTDATE DAX Function (Time Intelligence) Context Transition

Returns last non blank date.

## Syntax

LASTDATE ( <Dates> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Dates |  | The name of a column containing dates, a single column table containing dates, or a calendar reference. |

## Return values

Table A table with a single column.

## Notes

In order to use any time intelligence calculation, you need a well-formed date table. The *Date* table must satisfy the following requirements:

- All dates need to be present for the years required. The *Date* table must always start on January 1 and end on December 31, including all the days in this range. If the report only references fiscal years, then the date table must include all the dates from the first to the last day of a fiscal year. For example, if the fiscal year 2008 starts on July 1, 2007, then the *Date* table must include all the days from July 1, 2007 to June 30, 2008.
- There needs to be a column with a *DateTime* or *Date* data type containing unique values. This column is usually called *Date*. Even though the *Date* column is often used to define relationships with other tables, this is not required. Still, the *Date* column must contain unique values and should be referenced by the Mark as Date Table feature. In case the column also contains a time part, no time should be used – for example, the time should always be 12:00 am.
- The *Date* table must be marked as a date table in the model, in case the relationship between the *Date* table and any other table is not based on the *Date*.

The result of time intelligence functions has the same data lineage as the date column or table provided as an argument.

## Remarks

The dates argument can be any of the following:

- A reference to a date/time column. Only in this case a context transition applies because the column reference is replaced by
  - [[CALCULATETABLE]] ( [[DISTINCT]] ( <Dates> ) )
- A table expression that returns a single column of date/time values.
- A Boolean expression that defines a single-column table of date/time values.

The result table includes only dates that exist in the dates column.

The function [[MAX]] should be used instead of LASTDATE when the result must be a scalar value instead of a table.

[» 4 related articles](#articles)  
[» 3 related functions](#alt)  

## Examples

```dax


--  FIRSTDATE returns the minimum date selected, as a table.

--  LASTDATE returns the maximum date, still packed into a table.

DEFINE

VAR StartDate = DATE ( 2008, 08, 15 )

VAR EndDate   = DATE ( 2008, 08, 31 )



EVALUATE

    CALCULATETABLE (

        FIRSTDATE ( 'Date'[Date] ),

        'Date'[Date] >= StartDate && 'Date'[Date] <= EndDate

    )

    

EVALUATE

    CALCULATETABLE (

        LASTDATE ( 'Date'[Date] ),

        'Date'[Date] >= StartDate && 'Date'[Date] <= EndDate

    )

```

| Date |
| --- |
| 2008-08-15 |

| Date |
| --- |
| 2008-08-31 |

```dax


--  All time intelligence function are designed to return a table

--  to be easily used in CALCULATE as a filter.

DEFINE

    VAR StartDate = DATE ( 2008, 01, 01 )

    VAR EndDate   = DATE ( 2008, 01, 31 )

EVALUATE

CALCULATETABLE (

    { ( 

        CALCULATE ( 

            [Sales Amount], 

            FIRSTDATE ( 'Date'[Date] ) -- 2008-01-01

        ),

        CALCULATE ( 

            [Sales Amount], 

            LASTDATE ( 'Date'[Date] ) -- 2008-01-31

        )

    ) },

    'Date'[Date] >= StartDate

        && 'Date'[Date] <= EndDate

)



```

| Value1 | Value2 |
| --- | --- |
| 19,143.33 | 386.51 |

```dax


--  This example shows the sales in the current month

--  along with the sales in the first and last day of the month.

DEFINE

    MEASURE Sales[Sales first day] =

        CALCULATE (

            [Sales Amount],

            FIRSTDATE ( 'Date'[Date] )

        )

    MEASURE Sales[Sales last day] =

        CALCULATE (

            [Sales Amount],

            LASTDATE ( 'Date'[Date] )

        )

EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Calendar Year Month Number],

    "Sales Amount", [Sales Amount],

    "Sales first day", [Sales first day],

    "Sales last day", [Sales last day]

)

ORDER BY [Calendar Year Month Number]

```

| Calendar Year Month Number | Sales Amount | Sales first day | Sales last day |
| --- | --- | --- | --- |
| 200701 | 794,248.24 | (Blank) | 9,679.45 |
| 200702 | 891,135.91 | 29,563.13 | 2,626.20 |
| … | … | … | … |
| 200911 | 868,164.01 | 951.05 | 887.11 |
| 200912 | 746,933.50 | 18,046.77 | 40,930.59 |

## Related articles

Learn more about LASTDATE in the following articles:

- [**Semi-Additive Measures in DAX**](https://www.sqlbi.com/articles/semi-additive-measures-in-dax/)

  Values such as inventory and balance account, usually calculated from a snapshot table, require the use of semi-additive measures. In Multidimensional you have specific aggregation types, like LastChild and LastNonEmpty. In PowerPivot and Tabular you use DAX, which is flexible enough to implement any calculation, as described in this article. [» Read more](https://www.sqlbi.com/articles/semi-additive-measures-in-dax/)
- [**Understanding the difference between LASTDATE and MAX in DAX**](https://www.sqlbi.com/articles/understanding-the-difference-between-lastdate-and-max-in-dax/)

  This article explains why in many cases, MAX should be used instead of LASTDATE to search for the last date in a time period using DAX. [» Read more](https://www.sqlbi.com/articles/understanding-the-difference-between-lastdate-and-max-in-dax/)
- [**Differences between DATEADD and PARALLELPERIOD in DAX**](https://www.sqlbi.com/articles/differences-between-dateadd-and-parallelperiod-in-dax/)

  This article describes the difference between the results of DATEADD and PARALLELPERIOD in DAX. These differences also impact many other time intelligence functions that are syntax sugar of these two. [» Read more](https://www.sqlbi.com/articles/differences-between-dateadd-and-parallelperiod-in-dax/)
- [**Optimizing incremental inventory calculations in DAX**](https://www.sqlbi.com/articles/optimizing-incremental-inventory-calculations-in-dax/)

  This article describes how to optimize inventory calculations in DAX by using snapshots to avoid the computational cost of a complete running total. [» Read more](https://www.sqlbi.com/articles/optimizing-incremental-inventory-calculations-in-dax/)

## Related functions

Other related functions are:

- [[FIRSTNONBLANK]]
- [[LASTNONBLANK]]
- [[FIRSTDATE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/lastdate-function-dax](https://docs.microsoft.com/en-us/dax/lastdate-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
