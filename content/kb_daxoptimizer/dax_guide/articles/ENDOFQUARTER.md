---
title: "ENDOFQUARTER"
function: "endofquarter"
category: "Time Intelligence"
url: "https://dax.guide/endofquarter/"
source: "dax.guide"
重要度:
难度:
---

# ENDOFQUARTER DAX Function (Time Intelligence) Context Transition

Returns the end of quarter.

## Syntax

ENDOFQUARTER ( <Dates> )

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

The <Dates> argument can be any of the following:

- A reference to a date/time column. Only in this case a context transition applies because the column reference is replaced by
  - [[CALCULATETABLE]] ( [[DISTINCT]] ( <Dates> ) )
- A table expression that returns a single column of date/time values.
- A Boolean expression that defines a single-column table of date/time values (when used in a [[CALCULATE]] filter argument).

ENDOFQUARTER filters <Dates> into a 1-row, 1-column table that shows only the latest date, in the entire <Dates> column devoid of all filters, that is in the same year and quarter as the latest visible date in <Dates>.

The following ENDOFQUARTER call

```dax


ENDOFQUARTER ( 'Date'[date] )

```

is a more efficient implementation of the following semantically equivalent expression:

```dax


VAR LastDateVisible =

    CALCULATE ( MAX ( 'Date'[Date] ) )

VAR LastYearVisible =

    YEAR ( LastDateVisible )

VAR LastQuarterVisible =

    QUOTIENT ( MONTH ( LastDateVisible ) - 1, 3 )

VAR DaysInQuarter =

    FILTER (

        ALL ( 'Date'[Date] ),

        YEAR ( 'Date'[Date] ) = LastYearVisible

            && QUOTIENT ( MONTH ( 'Date'[Date] ) - 1, 3 ) = LastQuarterVisible

    )

VAR LastDayInQuarter =

    MAXX (

        DaysInQuarter,

        'Date'[Date]

    )

VAR Result =

    CALCULATETABLE (

        VALUES ( 'Date'[Date] ),

        'Date'[Date] = LastDayInQuarter

    )

RETURN

    Result

```

[» 1 related article](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  If the selection is a single date, the result is a table with

--  the requested date.

EVALUATE

CALCULATETABLE (

    STARTOFQUARTER ( 'Date'[Date] ),

    'Date'[Date] = DATE ( 2007, 5, 12 )

)



EVALUATE

CALCULATETABLE (

    ENDOFQUARTER ( 'Date'[Date] ),

    'Date'[Date] = DATE ( 2007, 5, 12 )

)



```

| Date |
| --- |
| 2007-04-01 |

| Date |
| --- |
| 2007-06-30 |

```dax


--  If the selection contains larger periods, it returns the beginning or

--  the end of the entire period.

--  The result is always a single-row table.

EVALUATE

    CALCULATETABLE ( 

        STARTOFQUARTER ( 'Date'[Date] ),

        'Date'[Date] >= DATE ( 2007, 2, 5 ) &&

        'Date'[Date] <= DATE ( 2007, 5, 12 ) 

    )

    

EVALUATE

    CALCULATETABLE ( 

        ENDOFQUARTER ( 'Date'[Date] ),

        'Date'[Date] >= DATE ( 2007, 2, 5 ) &&

        'Date'[Date] <= DATE ( 2007, 5, 12 ) 

    )

```

| Date |
| --- |
| 2007-01-01 |

| Date |
| --- |
| 2007-06-30 |

```dax


--  All time intelligence function are designed to return a table

--  to be easily used in CALCULATE as a filter.

EVALUATE

{

    CALCULATE (

        CALCULATE (

            [Sales Amount],

            ENDOFQUARTER ( 'Date'[Date] )  -- 2007-06-30

        ),

        'Date'[Date] = DATE ( 2007, 5, 12 )

    )

}

```

| Value |
| --- |
| 378.55 |

## Related articles

Learn more about ENDOFQUARTER in the following articles:

- [**Semi-Additive Measures in DAX**](https://www.sqlbi.com/articles/semi-additive-measures-in-dax/)

  Values such as inventory and account balance, usually calculated from a snapshot table, require the use of semi-additive measures. This article describes how to implement these calculations in DAX according to your specific requirements. [» Read more](https://www.sqlbi.com/articles/semi-additive-measures-in-dax/)

## Related functions

Other related functions are:

- [[ENDOFMONTH]]
- [[ENDOFYEAR]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/endofquarter-function-dax](https://docs.microsoft.com/en-us/dax/endofquarter-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
