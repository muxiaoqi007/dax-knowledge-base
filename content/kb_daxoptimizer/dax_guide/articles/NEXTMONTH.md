---
title: "NEXTMONTH"
function: "nextmonth"
category: "Time Intelligence"
url: "https://dax.guide/nextmonth/"
source: "dax.guide"
重要度:
难度:
---

# NEXTMONTH DAX Function (Time Intelligence) Context Transition

Returns a next month.

## Syntax

NEXTMONTH ( <Dates> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Dates |  | The name of a column containing dates, a single column table containing dates, or a calendar reference. |

## Return values

Table A table with a single column.

A table containing a single column of date values.

## Notes

In order to use any time intelligence calculation, you need a well-formed date table. The *Date* table must satisfy the following requirements:

- All dates need to be present for the years required. The *Date* table must always start on January 1 and end on December 31, including all the days in this range. If the report only references fiscal years, then the date table must include all the dates from the first to the last day of a fiscal year. For example, if the fiscal year 2008 starts on July 1, 2007, then the *Date* table must include all the days from July 1, 2007 to June 30, 2008.
- There needs to be a column with a *DateTime* or *Date* data type containing unique values. This column is usually called *Date*. Even though the *Date* column is often used to define relationships with other tables, this is not required. Still, the *Date* column must contain unique values and should be referenced by the Mark as Date Table feature. In case the column also contains a time part, no time should be used – for example, the time should always be 12:00 am.
- The *Date* table must be marked as a date table in the model, in case the relationship between the *Date* table and any other table is not based on the *Date*.

The result of time intelligence functions has the same data lineage as the date column or table provided as an argument.

## Remarks

The result includes all the days in the next month considering the last day in the dates argument.

The dates argument can be any of the following:

- A reference to a date/time column. Only in this case a context transition applies because the column reference is replaced by
  - [[CALCULATETABLE]] ( [[DISTINCT]] ( <Dates> ) )
- A table expression that returns a single column of date/time values.
- A Boolean expression that defines a single-column table of date/time values.

The result table includes only dates that exist in the dates column.

Internally, NEXTMONTH corresponds to the following call of [[PARALLELPERIOD]]:

```dax


PARALLELPERIOD ( LASTDATE ( <Dates> ), 1, MONTH )

```

[» 2 related articles](#articles)  
[» 7 related functions](#alt)  

## Examples

```dax


--  NEXTMONTH returns the full month after the last day in the selection

EVALUATE

CALCULATETABLE (

    NEXTMONTH ( 'Date'[Date] ),   

    'Date'[Date] >= DATE ( 2008, 08, 15 ) &&

    'Date'[Date] <= DATE ( 2008, 08, 20 ) 

)

ORDER BY [Date] ASC

```

| Date |
| --- |
| 2008-09-01 |
| 2008-09-02 |
| … |
| 2008-09-29 |
| 2008-09-30 |

```dax


--  All time intelligence function are designed to return a table

--  to be easily used in CALCULATE as a filter.

DEFINE

    VAR StartDate = DATE ( 2008, 08, 15 )

    VAR EndDate   = DATE ( 2008, 08, 20 )

EVALUATE

CALCULATETABLE (

    ROW (

        "Selection",

            [Sales Amount],

        "Next Month",

            CALCULATE (

                [Sales Amount],

                NEXTMONTH ( 'Date'[Date] ) -- 2008-09-01 : 2008-09-30

            )

    ),

    'Date'[Date] >= StartDate 

        && 'Date'[Date] <= EndDate

)

```

| Selection | Next Month |
| --- | --- |
| 91,636.30 | 963,437.23 |

## Related articles

Learn more about NEXTMONTH in the following articles:

- [**Differences between DATEADD and PARALLELPERIOD in DAX**](https://www.sqlbi.com/articles/differences-between-dateadd-and-parallelperiod-in-dax/)

  This article describes the difference between the results of DATEADD and PARALLELPERIOD in DAX. These differences also impact many other time intelligence functions that are syntax sugar of these two. [» Read more](https://www.sqlbi.com/articles/differences-between-dateadd-and-parallelperiod-in-dax/)
- [**Introducing calendar-based time intelligence in DAX**](https://www.sqlbi.com/articles/introducing-calendar-based-time-intelligence-in-dax/)

  This article introduces the new calendar-based time intelligence functions in DAX, available in preview from the September 2025 release of Power BI. [» Read more](https://www.sqlbi.com/articles/introducing-calendar-based-time-intelligence-in-dax/)

## Related functions

Other related functions are:

- [[NEXTDAY]]
- [[NEXTQUARTER]]
- [[NEXTYEAR]]
- [[PREVIOUSDAY]]
- [[PREVIOUSMONTH]]
- [[PREVIOUSQUARTER]]
- [[PREVIOUSYEAR]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Arek Falak, Poul Jørgensen

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/nextmonth-function-dax](https://docs.microsoft.com/en-us/dax/nextmonth-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
