---
title: "DATESBETWEEN"
function: "datesbetween"
category: "Time Intelligence"
url: "https://dax.guide/datesbetween/"
source: "dax.guide"
重要度:
难度:
---

# DATESBETWEEN DAX Function (Time Intelligence)

Returns the dates between two given dates.

## Syntax

DATESBETWEEN ( <Dates>, <StartDate>, <EndDate> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Dates |  | A column reference containing dates. |
| StartDate |  | Start date. |
| EndDate |  | End date. |

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

The dates argument must be a reference to a date/time column.  
The result table includes only dates that exist in the dates column.

If StartDate is a blank date value, then StartDate will be the earliest value in the dates column.

If EndDate is a blank date value, then EndDate will be the latest value in the dates column.

The dates used as the StartDate and EndDate are inclusive: that is, if the sales occurred on September 1 and you use September 1 as the start date, sales on September 1 are counted.

If StartDate is larger than EndDate, the result is an empty table.

[» 5 related articles](#articles)  

## Examples

```dax


--  DATESBETWEEN returns the dates between the boundaries specified.

--  The boundaries are both included in the result.

--  If EndDate is earlier than LastDate, the result is an empty table.

EVALUATE

VAR StartDate = DATE ( 2008, 08, 25 )

VAR EndDate =   DATE ( 2008, 08, 31 )

RETURN

    DATESBETWEEN ( 'Date'[Date], StartDate, EndDate )

ORDER BY [Date]

```

| Date |
| --- |
| 2008-08-25 |
| 2008-08-26 |
| 2008-08-27 |
| 2008-08-28 |
| 2008-08-29 |
| 2008-08-30 |
| 2008-08-31 |

```dax


--  Using BLANK for one boundary means use MIN/MAX of available dates.

--  This query returns all the dates from Jan 1, 2005 (first date in the Date table)

--  and August 31, 2008

EVALUATE

VAR StartDate = BLANK ()

VAR EndDate =   DATE ( 2008, 08, 31 )

RETURN

    DATESBETWEEN ( 'Date'[Date], StartDate, EndDate )

ORDER BY [Date]

```

```dax


--  DATESBETWEEN returns dates that exist in the Date table.

--  A date lower than the minimum makes DATESEBETWEEN start

--  from the min value anyway.

--  The content of the Date table starts from Jan 1, 2005.

EVALUATE

VAR StartDate = DATE ( 2004, 01, 01 )   -- Lower than MIN ( 'Date'[Date] )

VAR EndDate =   DATE ( 2005, 01, 6 )

RETURN

    DATESBETWEEN ( 'Date'[Date], StartDate, EndDate )

ORDER BY [Date]

```

| Date |
| --- |
| 2005-01-01 |
| 2005-01-02 |
| 2005-01-03 |
| 2005-01-04 |
| 2005-01-05 |
| 2005-01-06 |

```dax


--  In this example we compute the moving average of [Sales Amount] over 30 days

--  Moving periods are easier to compute using DATESINPERIOD, anyway.

DEFINE

    MEASURE Sales[Sales Last 30D] =

        VAR Last30D =

            DATESBETWEEN ( 

                'Date'[Date], 

                MAX ( 'Date'[Date] ) - 29,  -- boundaries are included, this is why we use 29

                MAX ( 'Date'[Date] )        -- to obtain 30 days

            )

        VAR Result =

            CALCULATE (

                [Sales Amount] / 30,

                Last30D

            )

        RETURN

            Result

EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Date],

    "Sales Amount", [Sales Amount],

    "Sales 30D", [Sales Last 30D]

)

ORDER BY [Date]

```

## Related articles

Learn more about DATESBETWEEN in the following articles:

- [**Counting working days in DAX**](https://www.sqlbi.com/articles/counting-working-days-in-dax/)

  This article shows a DAX technique to compute the number of working days between two dates. [» Read more](https://www.sqlbi.com/articles/counting-working-days-in-dax/)
- [**Differences between DATEADD and PARALLELPERIOD in DAX**](https://www.sqlbi.com/articles/differences-between-dateadd-and-parallelperiod-in-dax/)

  This article describes the difference between the results of DATEADD and PARALLELPERIOD in DAX. These differences also impact many other time intelligence functions that are syntax sugar of these two. [» Read more](https://www.sqlbi.com/articles/differences-between-dateadd-and-parallelperiod-in-dax/)
- [**Computing sales to specific customers before and after a time period**](https://www.sqlbi.com/articles/computing-sales-to-specific-customers-before-and-after-a-time-period/)

  This article shows how to manipulate the filter context to create a report with the sales made to a specific customer segment, before and after a selected month. [» Read more](https://www.sqlbi.com/articles/computing-sales-to-specific-customers-before-and-after-a-time-period/)
- [**Optimizing time intelligence in DirectQuery**](https://www.sqlbi.com/articles/optimizing-time-intelligence-in-directquery/)

  This article describes how to optimize time intelligence calculations with DirectQuery over SQL in Power BI by avoiding time intelligence DAX functions. [» Read more](https://www.sqlbi.com/articles/optimizing-time-intelligence-in-directquery/)
- [**Blank in date columns and DAX time intelligence functions**](https://www.sqlbi.com/articles/blank-in-date-columns-and-dax-time-intelligence-functions/)

  This article explores the implications of having blank values in date columns and provides the best practices for managing them in DAX calculations and Power BI reports. [» Read more](https://www.sqlbi.com/articles/blank-in-date-columns-and-dax-time-intelligence-functions/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/datesbetween-function-dax](https://docs.microsoft.com/en-us/dax/datesbetween-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
