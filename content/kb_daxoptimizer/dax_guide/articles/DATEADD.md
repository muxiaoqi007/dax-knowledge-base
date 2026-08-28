---
title: "DATEADD"
function: "dateadd"
category: "Time Intelligence"
url: "https://dax.guide/dateadd/"
source: "dax.guide"
重要度:
难度:
---

# DATEADD DAX Function (Time Intelligence) Context Transition

Moves the given set of dates by a specified interval.

## Syntax

DATEADD ( <Dates>, <NumberOfIntervals>, <Interval> [, <Extension>] [, <Truncation>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Dates |  | The name of a column containing dates, a single column table containing dates, or a calendar reference. |
| NumberOfIntervals |  | The number of intervals to shift. |
| Interval |  | One of: Day, Month, Quarter, Year. |
| Extension | Optional | An enumeration that defines DateAdd behavior when the original time period has fewer dates than the resulting time period. Valid values are: EXTENDING, PRECISE, ENDALIGNED. |
| Truncation | Optional | An enumeration that defines DateAdd behavior when the original time period has more dates than the resulting time period. Valid values are: ANCHORED, BLANKS. |

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

If the number specified for NumberOfIntervals is positive, the dates in dates are moved forward in time; if the number is negative, the dates in dates are shifted back in time.

The result table includes only dates that exist in the dates column.

The result table has an entire month if an entire month is selected in the source table. The notion of “month selected” is based on the dates available in the underlying date table. This way:

- If the <Dates> argument includes all the days in April (30 rows), the result for the previous month includes all the days in March (31 days).
- If the <Dates> argument includes the first 15 days in April (15 rows), the result for the previous month includes the first 15 days in March (15 rows).

[» 5 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  DATEADD is a more generic functions. 

--  It shifts a period back and forth over time using 

--  DAY, MONTH, QUARTER, YEAR

--  This example produces the same result as SAMEPERIODLASTYEAR

EVALUATE

VAR StartDate = DATE ( 2008, 07, 25 )

VAR EndDate =   DATE ( 2008, 07, 31 )

RETURN

    CALCULATETABLE (

        DATEADD ( 'Date'[Date], -1, YEAR ),

        'Date'[Date] >= StartDate &&

        'Date'[Date] <= EndDate

    )

ORDER BY [Date]

```

| Date |
| --- |
| 2007-07-25 |
| 2007-07-26 |
| 2007-07-27 |
| 2007-07-28 |
| 2007-07-29 |
| 2007-07-30 |
| 2007-07-31 |

```dax


--  DATEADD has a quite complex logic to move months and quarters

--  the right way, handling months with different dates.

EVALUATE

VAR StartDate = DATE ( 2008, 02, 25 )

VAR EndDate =   DATE ( 2008, 02, 29 )

RETURN

    CALCULATETABLE (

        DATEADD ( 'Date'[Date], +1, MONTH ),

        'Date'[Date] >= StartDate &&

        'Date'[Date] <= EndDate

    )

ORDER BY [Date]

```

| Date |
| --- |
| 2008-03-25 |
| 2008-03-26 |
| 2008-03-27 |
| 2008-03-28 |
| 2008-03-29 |
| 2008-03-30 |
| 2008-03-31 |

```dax


--  This example shows the sales in the current and previous month.

--  It also reports sales in the same month in the previous quarter and year.

DEFINE

    MEASURE Sales[Same period last month] =

        CALCULATE (

            [Sales Amount],

            DATEADD ( 'Date'[Date], -1, MONTH )

        )

    MEASURE Sales[Same period last quarter] =

        CALCULATE (

            [Sales Amount],

            DATEADD ( 'Date'[Date], -1, QUARTER )

        )

    MEASURE Sales[Same period last year] =

        CALCULATE (

            [Sales Amount],

            SAMEPERIODLASTYEAR ( 'Date'[Date] )

        )

EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Calendar Year Month Number],

    'Date'[Calendar Year Month],

    "Sales Amount", [Sales Amount],

    "Same period last month", [Same period last month],

    "Same period last quarter", [Same period last quarter],

    "Same period last year", [Same period last year]

)

ORDER BY [Calendar Year Month Number]

```

| Calendar Year Month Number | Calendar Year Month | Sales Amount | Same period last month | Same period last quarter | Same period last year |
| --- | --- | --- | --- | --- | --- |
| 200701 | 2007-01-01 | 794,248.24 | (Blank) | (Blank) | (Blank) |
| 200702 | 2007-02-01 | 891,135.91 | 794,248.24 | (Blank) | (Blank) |
| 200703 | 2007-03-01 | 961,289.24 | 891,135.91 | (Blank) | (Blank) |
| 200704 | 2007-04-01 | 1,128,104.82 | 961,289.24 | 794,248.24 | (Blank) |
| 200705 | 2007-05-01 | 936,192.74 | 1,128,104.82 | 891,135.91 | (Blank) |
| 200706 | 2007-06-01 | 982,304.46 | 936,192.74 | 961,289.24 | (Blank) |
| 200707 | 2007-07-01 | 922,542.98 | 982,304.46 | 1,128,104.82 | (Blank) |
| 200708 | 2007-08-01 | 952,834.58 | 922,542.98 | 936,192.74 | (Blank) |
| 200709 | 2007-09-01 | 1,009,868.98 | 952,834.59 | 982,304.46 | (Blank) |
| 200710 | 2007-10-01 | 914,273.54 | 1,009,868.98 | 922,542.98 | (Blank) |
| 200711 | 2007-11-01 | 825,601.87 | 914,273.54 | 952,834.59 | (Blank) |
| 200712 | 2007-12-01 | 991,548.75 | 825,601.87 | 1,009,868.98 | (Blank) |
| 200801 | 2008-01-01 | 656,766.69 | 991,548.75 | 914,273.54 | 794,248.24 |
| 200802 | 2008-02-01 | 600,080.00 | 656,766.69 | 825,601.87 | 891,135.91 |
| 200803 | 2008-03-01 | 559,538.52 | 600,080.00 | 991,548.75 | 961,289.24 |
| 200804 | 2008-04-01 | 999,667.17 | 559,538.52 | 656,766.69 | 1,128,104.82 |
| 200805 | 2008-05-01 | 893,231.96 | 999,667.17 | 600,080.00 | 936,192.74 |
| 200806 | 2008-06-01 | 845,141.60 | 893,231.96 | 559,538.52 | 982,304.46 |
| 200807 | 2008-07-01 | 890,547.41 | 845,141.60 | 999,667.17 | 922,542.98 |
| 200808 | 2008-08-01 | 721,560.95 | 890,547.41 | 893,231.96 | 952,834.59 |
| 200809 | 2008-09-01 | 963,437.23 | 721,560.95 | 845,141.60 | 1,009,868.98 |
| 200810 | 2008-10-01 | 719,792.99 | 963,437.23 | 890,547.41 | 914,273.54 |
| 200811 | 2008-11-01 | 1,156,109.32 | 719,792.99 | 721,560.95 | 825,601.87 |
| 200812 | 2008-12-01 | 921,709.14 | 1,156,109.32 | 963,437.23 | 991,548.75 |
| 200901 | 2009-01-01 | 580,901.05 | 921,709.14 | 719,792.99 | 656,766.69 |
| 200902 | 2009-02-01 | 622,581.14 | 580,901.05 | 1,156,109.32 | 600,080.00 |
| 200903 | 2009-03-01 | 496,137.87 | 622,581.14 | 921,709.14 | 559,538.52 |
| 200904 | 2009-04-01 | 678,893.22 | 496,137.87 | 580,901.05 | 999,667.17 |
| 200905 | 2009-05-01 | 1,067,165.23 | 678,893.22 | 622,581.14 | 893,231.96 |
| 200906 | 2009-06-01 | 872,586.20 | 1,067,165.23 | 496,137.87 | 845,141.60 |
| 200907 | 2009-07-01 | 1,068,396.58 | 872,586.20 | 678,893.22 | 890,547.41 |
| 200908 | 2009-08-01 | 835,707.46 | 1,068,396.58 | 1,067,165.23 | 721,560.95 |
| 200909 | 2009-09-01 | 709,610.40 | 835,707.46 | 872,586.20 | 963,437.23 |
| 200910 | 2009-10-01 | 806,738.22 | 709,610.40 | 1,068,396.58 | 719,792.99 |
| 200911 | 2009-11-01 | 868,164.01 | 806,738.22 | 835,707.46 | 1,156,109.32 |
| 200912 | 2009-12-01 | 746,933.50 | 868,164.01 | 709,610.40 | 921,709.14 |
| 201001 | 2010-01-01 | (Blank) | 746,933.50 | 806,738.22 | 580,901.05 |
| 201002 | 2010-02-01 | (Blank) | (Blank) | 868,164.01 | 622,581.14 |
| 201003 | 2010-03-01 | (Blank) | (Blank) | 746,933.50 | 496,137.87 |
| 201004 | 2010-04-01 | (Blank) | (Blank) | (Blank) | 678,893.22 |
| 201005 | 2010-05-01 | (Blank) | (Blank) | (Blank) | 1,067,165.23 |
| 201006 | 2010-06-01 | (Blank) | (Blank) | (Blank) | 872,586.20 |
| 201007 | 2010-07-01 | (Blank) | (Blank) | (Blank) | 1,068,396.58 |
| 201008 | 2010-08-01 | (Blank) | (Blank) | (Blank) | 835,707.46 |
| 201009 | 2010-09-01 | (Blank) | (Blank) | (Blank) | 709,610.40 |
| 201010 | 2010-10-01 | (Blank) | (Blank) | (Blank) | 806,738.22 |
| 201011 | 2010-11-01 | (Blank) | (Blank) | (Blank) | 868,164.01 |
| 201012 | 2010-12-01 | (Blank) | (Blank) | (Blank) | 746,933.50 |

## Related articles

Learn more about DATEADD in the following articles:

- [**Differences between DATEADD and PARALLELPERIOD in DAX**](https://www.sqlbi.com/articles/differences-between-dateadd-and-parallelperiod-in-dax/)

  This article describes the difference between the results of DATEADD and PARALLELPERIOD in DAX. These differences also impact many other time intelligence functions that are syntax sugar of these two. [» Read more](https://www.sqlbi.com/articles/differences-between-dateadd-and-parallelperiod-in-dax/)
- [**Optimizing time intelligence in DirectQuery**](https://www.sqlbi.com/articles/optimizing-time-intelligence-in-directquery/)

  This article describes how to optimize time intelligence calculations with DirectQuery over SQL in Power BI by avoiding time intelligence DAX functions. [» Read more](https://www.sqlbi.com/articles/optimizing-time-intelligence-in-directquery/)
- [**Filtering weekdays in DAX**](https://www.sqlbi.com/articles/filtering-weekdays-in-dax/)

  When using time intelligence functions, the automatic REMOVEFILTERS on Date can make maintaining filters on the Date table challenging. This article shows a technique to handle filter-preserving columns in DAX. [» Read more](https://www.sqlbi.com/articles/filtering-weekdays-in-dax/)
- [**Introducing calendar-based time intelligence in DAX**](https://www.sqlbi.com/articles/introducing-calendar-based-time-intelligence-in-dax/)

  This article introduces the new calendar-based time intelligence functions in DAX, available in preview from the September 2025 release of Power BI. [» Read more](https://www.sqlbi.com/articles/introducing-calendar-based-time-intelligence-in-dax/)
- [**Understanding DATEADD parameters with calendar-based time intelligence**](https://www.sqlbi.com/articles/understanding-dateadd-parameters-with-calendar-based-time-intelligence/)

  The new calendar-based time intelligence functions offer greater flexibility than the classic time intelligence functions. This article describes the DATEADD parameters for controlling different granularity shifts. [» Read more](https://www.sqlbi.com/articles/understanding-dateadd-parameters-with-calendar-based-time-intelligence/)

## Related functions

Other related functions are:

- [[PARALLELPERIOD]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/dateadd-function-dax](https://docs.microsoft.com/en-us/dax/dateadd-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
