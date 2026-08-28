---
title: "PREVIOUSYEAR"
function: "previousyear"
category: "Time Intelligence"
url: "https://dax.guide/previousyear/"
source: "dax.guide"
重要度:
难度:
---

# PREVIOUSYEAR DAX Function (Time Intelligence) Context Transition

Returns a previous year.

## Syntax

PREVIOUSYEAR ( <Dates> [, <YearEndDate>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Dates |  | The name of a column containing dates, a single column table containing dates, or a calendar reference. |
| YearEndDate | Optional | End of year date. |

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

The result includes all the days in the previous year considering the first day in the dates argument.

The dates argument can be any of the following:

- A reference to a date/time column. Only in this case a context transition applies because the column reference is replaced by
  - [[CALCULATETABLE]] ( [[DISTINCT]] ( <Dates> ) )
- A table expression that returns a single column of date/time values.
- A Boolean expression that defines a single-column table of date/time values.

The result table includes only dates that exist in the dates column.

Internally, PREVIOUSYEAR corresponds to the following call of [[PARALLELPERIOD]]:

```dax


PARALLELPERIOD ( FIRSTDATE ( <Dates> ), -1, YEAR )

```

[» 1 related article](#articles)  
[» 7 related functions](#alt)  

## Examples

```dax


--  PREVIOUSYEAR returns the full year before the first day in the selection

EVALUATE

CALCULATETABLE (

    PREVIOUSYEAR ( 'Date'[Date] ),   

    'Date'[Date] >= DATE ( 2008, 08, 15 ) &&

    'Date'[Date] <= DATE ( 2008, 08, 20 ) 

)

ORDER BY [Date] ASC

```

| Date |
| --- |
| 2007-01-01 |
| 2007-01-02 |
| … |
| 2007-12-30 |
| 2007-12-31 |

```dax


--  All time intelligence function are designed to return a table

--  to be easily used in CALCULATE as a filter.

EVALUATE

    VAR StartDate = DATE ( 2008, 08, 15 )

    VAR EndDate   = DATE ( 2008, 08, 20 )

RETURN

CALCULATETABLE (

    ROW (

        "Selection",

            [Sales Amount],

        "Previous Year",

            CALCULATE (

                [Sales Amount],

                PREVIOUSYEAR ( 'Date'[Date] ) -- 2007-01-01 : 2007-12-31

            )

    ),

    'Date'[Date] >= StartDate 

        && 'Date'[Date] <= EndDate

)



```

| Selection | Previous Year |
| --- | --- |
| 91,636.30 | 11,309,946.12 |

```dax


--  The query returns the current sales, the sales in the previous and next month

--  and the sales in the full previous year

DEFINE

    MEASURE Sales[Sales YTD] =

        CALCULATE ( [Sales Amount], DATESYTD ( 'Date'[Date] ) )

    MEASURE Sales[Sales Prev Year] =

        CALCULATE ( [Sales Amount], PREVIOUSYEAR ( 'Date'[Date] ) )

EVALUATE

SUMMARIZECOLUMNS ( 

    'Date'[Month], 'Date'[Month Number],

    TREATAS ( { "CY 2009" }, 'Date'[Calendar Year] ),

    "Current Sales", [Sales Amount],

    "Sales YTD", [Sales YTD],

    "Sales Prev Year", [Sales Prev Year],

    "YTD %", DIVIDE ( [Sales YTD], [Sales Prev Year] )

)

ORDER BY

    [Month Number]

```

| Month | Month Number | Current Sales | Sales YTD | Sales Prev Year | YTD % |
| --- | --- | --- | --- | --- | --- |
| January | 1 | 580,901.05 | 580,901.05 | 9,927,582.99 | 5.85% |
| February | 2 | 622,581.14 | 1,203,482.19 | 9,927,582.99 | 12.12% |
| March | 3 | 496,137.87 | 1,699,620.05 | 9,927,582.99 | 17.12% |
| April | 4 | 678,893.22 | 2,378,513.27 | 9,927,582.99 | 23.96% |
| May | 5 | 1,067,165.23 | 3,445,678.50 | 9,927,582.99 | 34.71% |
| June | 6 | 872,586.20 | 4,318,264.70 | 9,927,582.99 | 43.50% |
| July | 7 | 1,068,396.58 | 5,386,661.27 | 9,927,582.99 | 54.26% |
| August | 8 | 835,707.46 | 6,222,368.73 | 9,927,582.99 | 62.68% |
| September | 9 | 709,610.40 | 6,931,979.13 | 9,927,582.99 | 69.83% |
| October | 10 | 806,738.22 | 7,738,717.35 | 9,927,582.99 | 77.95% |
| November | 11 | 868,164.01 | 8,606,881.36 | 9,927,582.99 | 86.70% |
| December | 12 | 746,933.50 | 9,353,814.87 | 9,927,582.99 | 94.22% |

## Related articles

Learn more about PREVIOUSYEAR in the following articles:

- [**Differences between DATEADD and PARALLELPERIOD in DAX**](https://www.sqlbi.com/articles/differences-between-dateadd-and-parallelperiod-in-dax/)

  This article describes the difference between the results of DATEADD and PARALLELPERIOD in DAX. These differences also impact many other time intelligence functions that are syntax sugar of these two. [» Read more](https://www.sqlbi.com/articles/differences-between-dateadd-and-parallelperiod-in-dax/)

## Related functions

Other related functions are:

- [[NEXTDAY]]
- [[NEXTMONTH]]
- [[NEXTQUARTER]]
- [[NEXTYEAR]]
- [[PREVIOUSDAY]]
- [[PREVIOUSMONTH]]
- [[PREVIOUSQUARTER]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/previousyear-function-dax](https://docs.microsoft.com/en-us/dax/previousyear-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
