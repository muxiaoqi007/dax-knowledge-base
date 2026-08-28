---
title: "DATESMTD"
function: "datesmtd"
category: "Time Intelligence"
url: "https://dax.guide/datesmtd/"
source: "dax.guide"
重要度:
难度:
---

# DATESMTD DAX Function (Time Intelligence) Context Transition

Returns a set of dates in the month up to the last date visible in the filter context.

## Syntax

DATESMTD ( <Dates> )

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

The dates argument can be any of the following:

- A reference to a date/time column. Only in this case a context transition applies because the column reference is replaced by
  - [[CALCULATETABLE]] ( [[DISTINCT]] ( <Dates> ) )
- A table expression that returns a single column of date/time values.
- A Boolean expression that defines a single-column table of date/time values.

The result table includes only dates that exist in the dates column.

The syntax:

```dax


DATESMTD ( <Dates> ) 

```

corresponds to:

```dax


DATESBETWEEN ( 

    <Dates>, 

    STARTOFMONTH ( LASTDATE ( <Dates> ) ),

    LASTDATE ( <Dates> ) 

)

```

[» 1 related article](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  DATESMTD returns the dates from the first day of the currently selected

--  month to the last date visible in the filter context.

EVALUATE

CALCULATETABLE (

    DATESMTD ( 'Date'[Date] ),

    'Date'[Date] = DATE ( 2007, 5, 6 )

)

ORDER BY [Date] ASC

```

| Date |
| --- |
| 2007-05-01 |
| 2007-05-02 |
| 2007-05-03 |
| 2007-05-04 |
| 2007-05-05 |
| 2007-05-06 |

```dax


--  If the selection contains larger periods, it returns the MTD using

--  the end of the entire period.

--  The result is always a single-row table.

EVALUATE

    CALCULATETABLE ( 

        DATESMTD ( 'Date'[Date] ),

        'Date'[Date] >= DATE ( 2007, 2, 5 ) 

            && 'Date'[Date] <= DATE ( 2007, 5, 6 ) 

    )

ORDER BY [Date] ASC

```

| Date |
| --- |
| 2007-05-01 |
| 2007-05-02 |
| 2007-05-03 |
| 2007-05-04 |
| 2007-05-05 |
| 2007-05-06 |

```dax


--  All time intelligence function are designed to return a table

--  to be easily used in CALCULATE as a filter.

EVALUATE

{

    CALCULATE (

        CALCULATE (

            [Sales Amount],

            DATESMTD ( 'Date'[Date] )       -- 2007-05-01 : 2007-05-06

        ),

        'Date'[Date] = DATE ( 2007, 5, 6 )

    )

}

```

| Value |
| --- |
| 90,580.47 |

## Related articles

Learn more about DATESMTD in the following articles:

- [**Time Intelligence in Power BI Desktop**](https://www.sqlbi.com/articles/time-intelligence-in-power-bi-desktop/)

  In Power BI Desktop (as of February 2016) you have to use DAX to apply calculations over dates (such as year-to-date, year-over-year, and others), but you do not have the Mark as Date Table feature. This article describes which scenarios are impacted and the possible workarounds. [» Read more](https://www.sqlbi.com/articles/time-intelligence-in-power-bi-desktop/)

## Related functions

Other related functions are:

- [[DATESQTD]]
- [[DATESYTD]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/datesmtd-function-dax](https://docs.microsoft.com/en-us/dax/datesmtd-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
