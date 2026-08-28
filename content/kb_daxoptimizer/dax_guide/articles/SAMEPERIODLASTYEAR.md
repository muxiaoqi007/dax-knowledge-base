---
title: "SAMEPERIODLASTYEAR"
function: "sameperiodlastyear"
category: "Time Intelligence"
url: "https://dax.guide/sameperiodlastyear/"
source: "dax.guide"
重要度:
难度:
---

# SAMEPERIODLASTYEAR DAX Function (Time Intelligence) Context Transition

Returns a set of dates in the current selection from the previous year.

## Syntax

SAMEPERIODLASTYEAR ( <Dates> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Dates |  | The name of a column containing dates, a single column table containing dates, or a calendar reference. |

## Return values

Table A table with a single column.

The corresponding dates in the previous year.

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

The result table includes only dates that exist in the dates column.

Internally SAMEPERIODLASTYEAR corresponds to the following call of [[DATEADD]]:

```dax


DATEADD ( <Dates>, -1, YEAR )

```

[» 6 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  SAMEPERIODLASTYEAR returns the selected period shifted back one year.

EVALUATE

VAR StartDate = DATE ( 2008, 07, 25 )

VAR EndDate =   DATE ( 2008, 07, 31 )

RETURN

    CALCULATETABLE (

        SAMEPERIODLASTYEAR ( 'Date'[Date] ),

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


--  All time intelligence functions are designed to return a table

--  to be easily used in CALCULATE as a filter.

EVALUATE

VAR StartDate = DATE ( 2008, 01, 01 )

VAR EndDate =   DATE ( 2008, 01, 31 )

RETURN

{

    CALCULATE (

        CALCULATE (

            [Sales Amount],

            SAMEPERIODLASTYEAR ( 'Date'[Date] )  -- 2007-01-01 : 2007-01-31

        ),

        'Date'[Date] >= StartDate &&

        'Date'[Date] <= EndDate

    )

}

```

| Value |
| --- |
| 794,248.24 |

## Related articles

Learn more about SAMEPERIODLASTYEAR in the following articles:

- [**Differences between DATEADD and PARALLELPERIOD in DAX**](https://www.sqlbi.com/articles/differences-between-dateadd-and-parallelperiod-in-dax/)

  This article describes the difference between the results of DATEADD and PARALLELPERIOD in DAX. These differences also impact many other time intelligence functions that are syntax sugar of these two. [» Read more](https://www.sqlbi.com/articles/differences-between-dateadd-and-parallelperiod-in-dax/)
- [**Optimizing time intelligence in DirectQuery**](https://www.sqlbi.com/articles/optimizing-time-intelligence-in-directquery/)

  This article describes how to optimize time intelligence calculations with DirectQuery over SQL in Power BI by avoiding time intelligence DAX functions. [» Read more](https://www.sqlbi.com/articles/optimizing-time-intelligence-in-directquery/)
- [**Using weekly calendars in Power BI**](https://www.sqlbi.com/articles/using-weekly-calendars-in-power-bi/)

  This article describes why week-based calendars (like 4-4-5) are important for specific industries and how to use them effectively in Power BI. [» Read more](https://www.sqlbi.com/articles/using-weekly-calendars-in-power-bi/)
- [**Building bullet charts in Power BI reports**](https://www.sqlbi.com/articles/building-bullet-charts-in-power-bi-reports/)

  This article is about how to read and use bullet charts when comparing actuals to a target in Power BI, and the different options you have available to make these charts in Power BI reports. [» Read more](https://www.sqlbi.com/articles/building-bullet-charts-in-power-bi-reports/)
- [**Introducing calendar-based time intelligence in DAX**](https://www.sqlbi.com/articles/introducing-calendar-based-time-intelligence-in-dax/)

  This article introduces the new calendar-based time intelligence functions in DAX, available in preview from the September 2025 release of Power BI. [» Read more](https://www.sqlbi.com/articles/introducing-calendar-based-time-intelligence-in-dax/)
- [**Analyzing the performance impact of visual calculations**](https://www.sqlbi.com/articles/analyzing-the-performance-impact-of-visual-calculations/)

  Visual calculations can either improve or degrade the performance of a report. In this article, we outline how we ensure visual calculations have a positive impact on performance. [» Read more](https://www.sqlbi.com/articles/analyzing-the-performance-impact-of-visual-calculations/)

## Related functions

Other related functions are:

- [[DATEADD]]
- [[PARALLELPERIOD]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/sameperiodlastyear-function-dax](https://docs.microsoft.com/en-us/dax/sameperiodlastyear-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
