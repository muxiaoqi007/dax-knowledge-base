---
title: "PARALLELPERIOD"
function: "parallelperiod"
category: "Time Intelligence"
url: "https://dax.guide/parallelperiod/"
source: "dax.guide"
重要度:
难度:
---

# PARALLELPERIOD DAX Function (Time Intelligence) Context Transition

Returns a parallel period of dates by the given set of dates and a specified interval.

## Syntax

PARALLELPERIOD ( <Dates>, <NumberOfIntervals>, <Interval> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Dates |  | The name of a column containing dates, a single column table containing dates, or a calendar reference. |
| NumberOfIntervals |  | The number of the intervals. |
| Interval |  | One of: [[MONTH]], [[QUARTER]], [[YEAR]]. |

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

- A reference to a date/time column. Only in this case a context transition applies because the <Dates> column reference is replaced by
  - [[CALCULATETABLE]] ( [[DISTINCT]] ( <Dates> ) )
- A table expression that returns a single column of date/time values.
- A Boolean expression that defines a single-column table of date/time values.

This function takes the current set of dates in the column specified by Dates, shifts the first date and the last date the specified number of intervals, and then returns all contiguous dates between the two shifted dates. If the interval is a partial range of month, quarter, or year then any partial months in the result are also filled out to complete the entire interval.

The result table includes only dates that exist in the dates column.

[» 2 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  PARALLELPERIOD returns a full period shifted in time

EVALUATE

    CALCULATETABLE (

        PARALLELPERIOD ( 'Date'[Date], -1, YEAR ),

        'Date'[Date] = DATE ( 2008, 08, 15 )

    )



```

| Date |
| --- |
| 2007-01-01 |
| 2007-01-02 |
| … |
| 2007-12-30 |
| 2007-12-31 |

```dax


--  A common usage of PARALLELPERIOD is to use zero for the offset,

--  in that case PARALLELPERIOD extends the selection to the period

EVALUATE

    CALCULATETABLE (

        PARALLELPERIOD ( 'Date'[Date], 0, MONTH ),

        'Date'[Date] = DATE ( 2008, 08, 15 )

    )



```

| Date |
| --- |
| 2008-08-01 |
| 2008-08-02 |
| … |
| 2008-08-30 |
| 2008-08-31 |

```dax


--

--  When used with a period PARALLELPERIOD:

--      Computes the min and max dates in the selection

--      Extends min and max to the entire period defined

--      Shifts the result according to the offset

--

--  In the example:

--      Starting period: 2008-08-15, 2008-09-20

--      Extended period: 2008-08-01 : 2008-09-30

--      Shifted period:  2008-09-01 : 2008-10-31

EVALUATE

    CALCULATETABLE (

        PARALLELPERIOD ( 'Date'[Date], +1, MONTH ),

        OR (

            'Date'[Date] = DATE ( 2008, 08, 15 ), 

            'Date'[Date] = DATE ( 2008, 09, 20 )

        )

    )





```

| Date |
| --- |
| 2008-09-01 |
| 2008-09-02 |
| … |
| 2008-10-30 |
| 2008-10-31 |

```dax


--  Report showing the result of sales in the current month,

--  current quarter and previous quarter

DEFINE

    MEASURE Sales[Sales Current Quarter] =

        CALCULATE ( 

            [Sales Amount], 

            PARALLELPERIOD ( 'Date'[Date], 0, QUARTER ) 

        )

    MEASURE Sales[Sales Prev Quarter] =

        CALCULATE ( 

            [Sales Amount], 

            PARALLELPERIOD ( 'Date'[Date], -1, QUARTER ) 

        )

EVALUATE

CALCULATETABLE (

    ADDCOLUMNS (

        SUMMARIZE ( Sales, 'Date'[Calendar Year], 'Date'[Month], 'Date'[Month Number] ),

        "Current Sales", [Sales Amount],

        "Sales Current Quarter", [Sales Current Quarter],

        "Sales Prev Quarter", [Sales Prev Quarter]

    ),

    'Date'[Calendar Year] = "CY 2007",

    'Date'[Month Number] <= 9

)

ORDER BY

    [Calendar Year], [Month Number]

```

| Calendar Year | Month | Month Number | Current Sales | Sales Current Quarter | Sales Prev Quarter |
| --- | --- | --- | --- | --- | --- |
| 2007-01-01 | January | 1 | 794,248.24 | 2,646,673.39 | (Blank) |
| 2007-01-01 | February | 2 | 891,135.91 | 2,646,673.39 | (Blank) |
| 2007-01-01 | March | 3 | 961,289.24 | 2,646,673.39 | (Blank) |
| 2007-01-01 | April | 4 | 1,128,104.82 | 3,046,602.02 | 2,646,673.39 |
| 2007-01-01 | May | 5 | 936,192.74 | 3,046,602.02 | 2,646,673.39 |
| 2007-01-01 | June | 6 | 982,304.46 | 3,046,602.02 | 2,646,673.39 |
| 2007-01-01 | July | 7 | 922,542.98 | 2,885,246.55 | 3,046,602.02 |
| 2007-01-01 | August | 8 | 952,834.58 | 2,885,246.55 | 3,046,602.02 |
| 2007-01-01 | September | 9 | 1,009,868.98 | 2,885,246.55 | 3,046,602.02 |

## Related articles

Learn more about PARALLELPERIOD in the following articles:

- [**Differences between DATEADD and PARALLELPERIOD in DAX**](https://www.sqlbi.com/articles/differences-between-dateadd-and-parallelperiod-in-dax/)

  This article describes the difference between the results of DATEADD and PARALLELPERIOD in DAX. These differences also impact many other time intelligence functions that are syntax sugar of these two. [» Read more](https://www.sqlbi.com/articles/differences-between-dateadd-and-parallelperiod-in-dax/)
- [**Computing MTD, QTD, YTD in Power BI for the current period**](https://www.sqlbi.com/articles/computing-mtd-qtd-ytd-in-power-bi-for-the-current-period/)

  This article describes how to use the DAX time intelligence calculations applied to the latest period available in the data, also known as the “current” period. [» Read more](https://www.sqlbi.com/articles/computing-mtd-qtd-ytd-in-power-bi-for-the-current-period/)

## Related functions

Other related functions are:

- [[DATEADD]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/parallelperiod-function-dax](https://docs.microsoft.com/en-us/dax/parallelperiod-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
