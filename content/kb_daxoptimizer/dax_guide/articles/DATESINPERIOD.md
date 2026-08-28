---
title: "DATESINPERIOD"
function: "datesinperiod"
category: "Time Intelligence"
url: "https://dax.guide/datesinperiod/"
source: "dax.guide"
重要度:
难度:
---

# DATESINPERIOD DAX Function (Time Intelligence)

Returns the dates from the given period.

## Syntax

DATESINPERIOD ( <Dates>, <StartDate>, <NumberOfIntervals>, <Interval> [, <EndBehavior>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Dates |  | A column reference containing dates. |
| StartDate |  | Start date. |
| NumberOfIntervals |  | The number of intervals. |
| Interval |  | One of: Day, Week, Month, Quarter, Year. |
| EndBehavior | Optional | For calendar time intelligence only. An enumeration that defines DatesInPeriod behavior for endpoint and interval calculation. Valid values are: PRECISE, ENDALIGNED. Default value is PRECISE. |

## Return values

Table A table with a single column.

A table containing a single column of unique date values.

## Notes

In order to use any time intelligence calculation, you need a well-formed date table. The *Date* table must satisfy the following requirements:

- All dates need to be present for the years required. The *Date* table must always start on January 1 and end on December 31, including all the days in this range. If the report only references fiscal years, then the date table must include all the dates from the first to the last day of a fiscal year. For example, if the fiscal year 2008 starts on July 1, 2007, then the *Date* table must include all the days from July 1, 2007 to June 30, 2008.
- There needs to be a column with a *DateTime* or *Date* data type containing unique values. This column is usually called *Date*. Even though the *Date* column is often used to define relationships with other tables, this is not required. Still, the *Date* column must contain unique values and should be referenced by the Mark as Date Table feature. In case the column also contains a time part, no time should be used – for example, the time should always be 12:00 am.
- The *Date* table must be marked as a date table in the model, in case the relationship between the *Date* table and any other table is not based on the *Date*.

The result of time intelligence functions has the same data lineage as the date column or table provided as an argument.

## Remarks

The dates argument must be a reference to a date/time column.  
The result table includes only dates that exist in the dates column.  
A [[BLANK]] value provided as StartDate uses [[MIN]] ( Dates ) as StartDate argument.

For performance reasons, consider [[WINDOW]] instead of DATESINPERIOD whenever possible to take advantage of [WINDOW optimization](https://docs.sqlbi.com/dax-internals/optimization-notes/window-optimization).

[» 6 related articles](#articles)  

## Examples

The following expression evaluates the measure Sales Amount in the last 12 months starting from the last day of the period in the filter context.

```dax


Sales Moving Annual Total =

CALCULATE (

    [Sales Amount],

    DATESINPERIOD (

        'Date'[Date],

        MAX ( 'Date'[Date] ),

        -1,

        YEAR

    )

)

```

```dax


--  DATESINPERIOD returns an entire period, given a reference date and

--  the period.



--  The first query returns 1 day, starting from August 15, 2008

EVALUATE

    DATESINPERIOD ( 

        'Date'[Date],           -- Return dates in Date[Date]

        DATE ( 2008, 08, 15 ),  -- Starting from 08/15/2008

        1,                      -- the set needs to contain 1

        DAY                     -- day

    )

    

--  The second query returns 3 days, starting from August 15, 2008

EVALUATE

    DATESINPERIOD ( 

        'Date'[Date],           -- Return dates in Date[Date]

        DATE ( 2008, 08, 15 ),  -- Starting from 08/15/2008

        3,                      -- the set needs to contain 3

        DAY                     -- days

    )



--  The second query returns an empty table because we reques 0 days

EVALUATE

    DATESINPERIOD ( 

        'Date'[Date],           -- Return dates in Date[Date]

        DATE ( 2008, 08, 15 ),  -- Starting from 08/15/2008

        0,                      -- the set needs to contain 0

        DAY                     -- days

    )



```

| Date |
| --- |
| 2008-08-15 |

| Date |
| --- |
| 2008-08-15 |
| 2008-08-16 |
| 2008-08-17 |

```dax


--  When the offset is negative, DATESINPERIOD goes back to find

--  the dates to use



--  The first query returns 2 days, the last one is August 15, 2008

EVALUATE

    DATESINPERIOD ( 

        'Date'[Date],           -- Return dates in Date[Date]

        DATE ( 2008, 08, 15 ),  -- Starting from 08/15/2008

        -2,                     -- the set needs to contain 2

        DAY                     -- days, going back in time

    )

    

--  The second query returns an entire month (31 days), the last day is August 15, 2008.

--  The number of days for a month might vary between 28 and 31, depending on the month.

EVALUATE

    DATESINPERIOD ( 

        'Date'[Date],           -- Return dates in Date[Date]

        DATE ( 2008, 08, 15 ),  -- Starting from 08/15/2008

        -1,                     -- going back one

        MONTH                   -- month

    )

```

| Date |
| --- |
| 2008-08-14 |
| 2008-08-15 |

| Date |
| --- |
| 2008-07-16 |
| 2008-07-17 |
| 2008-07-18 |
| 2008-07-19 |
| 2008-07-20 |
| 2008-07-21 |
| 2008-07-22 |
| 2008-07-23 |
| 2008-07-24 |
| 2008-07-25 |
| 2008-07-26 |
| 2008-07-27 |
| 2008-07-28 |
| 2008-07-29 |
| 2008-07-30 |
| 2008-07-31 |
| 2008-08-01 |
| 2008-08-02 |
| 2008-08-03 |
| 2008-08-04 |
| 2008-08-05 |
| 2008-08-06 |
| 2008-08-07 |
| 2008-08-08 |
| 2008-08-09 |
| 2008-08-10 |
| 2008-08-11 |
| 2008-08-12 |
| 2008-08-13 |
| 2008-08-14 |
| 2008-08-15 |

```dax


--

--  In this example we compute the moving annual average of [Sales Amount]

--  only when DATESINPERIOD contains 12 months of sales

--

DEFINE

    MEASURE Sales[Sales MAT] =

        VAR OneYearBack =

            DATESINPERIOD ( 'Date'[Date], MAX ( 'Date'[Date] ), -1, YEAR )

        VAR Result =

            CALCULATE (

                VAR NumberOfMonths =

                    COUNTROWS ( SUMMARIZE ( Sales, 'Date'[Calendar Year Month Number] ) )

                VAR SalesMAT = [Sales Amount]

                VAR Result   = IF ( NumberOfMonths = 12, SalesMAT / NumberOfMonths )

                RETURN

                    Result,

                OneYearBack

            )

        RETURN

            Result

EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Calendar Year Month Number],

    'Date'[Calendar Year Month],

    TREATAS ( { "CY 2008" }, 'Date'[Calendar Year] ),

    "Sales Amount", [Sales Amount],

    "Sales MAT", [Sales MAT]

)

ORDER BY [Calendar Year Month Number]

```

| Calendar Year Month Number | Calendar Year Month | Sales Amount | Sales MAT |
| --- | --- | --- | --- |
| 200801 | 2008-01-01 | 656,766.69 | 931,038.71 |
| 200802 | 2008-02-01 | 600,080.00 | 906,784.06 |
| 200803 | 2008-03-01 | 559,538.52 | 873,304.83 |
| 200804 | 2008-04-01 | 999,667.17 | 862,601.69 |
| 200805 | 2008-05-01 | 893,231.96 | 859,021.63 |
| 200806 | 2008-06-01 | 845,141.60 | 847,591.39 |
| 200807 | 2008-07-01 | 890,547.41 | 844,925.09 |
| 200808 | 2008-08-01 | 721,560.95 | 825,652.29 |
| 200809 | 2008-09-01 | 963,437.23 | 821,782.97 |
| 200810 | 2008-10-01 | 719,792.99 | 805,576.26 |
| 200811 | 2008-11-01 | 1,156,109.32 | 833,118.55 |
| 200812 | 2008-12-01 | 921,709.14 | 827,298.58 |

## Related articles

Learn more about DATESINPERIOD in the following articles:

- [**Yearly Customer Historical Sales in DAX**](https://www.sqlbi.com/articles/yearly-customer-historical-sales-in-dax/)

  With DAX you can calculate the sales of the first, second and third year of a new customer without any ETL. In this article you see how to implement this calculation with good performance. [» Read more](https://www.sqlbi.com/articles/yearly-customer-historical-sales-in-dax/)
- [**Show previous 6 months of data from single slicer selection**](https://www.sqlbi.com/articles/show-previous-6-months-of-data-from-single-slicer-selection/)

  In this article we demonstrate how to use calculation groups to show the behavior of any measure in the last 6 months, starting from a single date selection with a slicer. This can be applied to any number of months. [» Read more](https://www.sqlbi.com/articles/show-previous-6-months-of-data-from-single-slicer-selection/)
- [**Rolling 12 Months Average in DAX**](https://www.sqlbi.com/articles/rolling-12-months-average-in-dax/)

  Rolling averages over time (a.k.a. moving averages or running averages) are useful to smoothen chart lines and to make trends more evident. This article shows how to compute a rolling average over 12 months, in DAX. [» Read more](https://www.sqlbi.com/articles/rolling-12-months-average-in-dax/)
- [**Blank in date columns and DAX time intelligence functions**](https://www.sqlbi.com/articles/blank-in-date-columns-and-dax-time-intelligence-functions/)

  This article explores the implications of having blank values in date columns and provides the best practices for managing them in DAX calculations and Power BI reports. [» Read more](https://www.sqlbi.com/articles/blank-in-date-columns-and-dax-time-intelligence-functions/)
- [**Understanding DATEADD parameters with calendar-based time intelligence**](https://www.sqlbi.com/articles/understanding-dateadd-parameters-with-calendar-based-time-intelligence/)

  The new calendar-based time intelligence functions offer greater flexibility than the classic time intelligence functions. This article describes the DATEADD parameters for controlling different granularity shifts. [» Read more](https://www.sqlbi.com/articles/understanding-dateadd-parameters-with-calendar-based-time-intelligence/)
- [**Using REMOVEFILTERS in DAX user-defined functions**](https://www.sqlbi.com/articles/using-removefilters-in-dax-user-defined-functions/)

  In this article, we implement a function that removes filter-keep column filters from a calendar, using REMOVEFILTERS as the return value of the function. [» Read more](https://www.sqlbi.com/articles/using-removefilters-in-dax-user-defined-functions/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Bernat Agullo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/datesinperiod-function-dax](https://docs.microsoft.com/en-us/dax/datesinperiod-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
