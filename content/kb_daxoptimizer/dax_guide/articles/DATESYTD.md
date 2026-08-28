---
title: "DATESYTD"
function: "datesytd"
category: "Time Intelligence"
url: "https://dax.guide/datesytd/"
source: "dax.guide"
重要度:
难度:
---

# DATESYTD DAX Function (Time Intelligence) Context Transition

Returns a set of dates in the year up to the last date visible in the filter context.

## Syntax

DATESYTD ( <Dates> [, <YearEndDate>] )

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

The dates argument can be any of the following:

- A reference to a date/time column. Only in this case a context transition applies because the column reference is replaced by
  - [[CALCULATETABLE]] ( [[DISTINCT]] ( <Dates> ) )
- A table expression that returns a single column of date/time values.
- A Boolean expression that defines a single-column table of date/time values.

The result table includes only dates that exist in the dates column.

The syntax:

```dax


DATESYTD ( <Dates>[, <YearEndDate>] ) 

```

corresponds to:

```dax


DATESBETWEEN ( 

    <Dates>, 

    STARTOFYEAR ( LASTDATE ( <Dates> )[, <YearEndDate>] ),

    LASTDATE ( <Dates> ) 

)

```

[» 7 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  DATESYTD returns the dates from the first of January of the currently selected date 

EVALUATE

CALCULATETABLE (

    DATESYTD ( 'Date'[Date] ),

    'Date'[Date] = DATE ( 2007, 5, 12 )

)

ORDER BY [Date] ASC

```

| Date |
| --- |
| 2007-01-01 |
| 2007-01-02 |
| … |
| 2007-05-11 |
| 2007-05-12 |

```dax


--  If the selection contains larger periods, it returns the YTD using

--  the end of the entire period.

--  The result is always a single-row table.

EVALUATE

    CALCULATETABLE ( 

        DATESYTD ( 'Date'[Date] ),

        'Date'[Date] >= DATE ( 2007, 2, 5 ) 

            && 'Date'[Date] <= DATE ( 2007, 5, 12 ) 

    )

ORDER BY [Date] ASC

```

| Date |
| --- |
| 2007-01-01 |
| 2007-01-02 |
| … |
| 2007-05-11 |
| 2007-05-12 |

```dax


--  All time intelligence function are designed to return a table

--  to be easily used in CALCULATE as a filter.

EVALUATE

{

    CALCULATE (

        CALCULATE (

            [Sales Amount],

            DATESYTD ( 'Date'[Date] )       -- 2007-01-01 : 2007-05-12

        ),

        'Date'[Date] = DATE ( 2007, 5, 12 )

    )

}

```

| Value |
| --- |
| 4,038,742.76 |

```dax


--  This example shows the sales in the current and both the

--  quarter-to-date and year-to-date sales.

DEFINE

    MEASURE Sales[Sales QTD] =

        CALCULATE (

            [Sales Amount],

            DATESQTD ( 'Date'[Date] )

        )

    MEASURE Sales[Sales YTD] =

        CALCULATE (

            [Sales Amount],

            DATESYTD ( 'Date'[Date] )

        )

EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Calendar Year Month Number],

    'Date'[Calendar Year Month],

    "Sales Amount", [Sales Amount],

    "Sales QTD", [Sales QTD],

    "Sales YTD", [Sales YTD]

)

ORDER BY [Calendar Year Month Number]

```

| Calendar Year Month Number | Calendar Year Month | Sales Amount | Sales QTD | Sales YTD |
| --- | --- | --- | --- | --- |
| 200701 | 2007-01-01 | 794,248.24 | 794,248.24 | 794,248.24 |
| 200702 | 2007-02-01 | 891,135.91 | 1,685,384.15 | 1,685,384.15 |
| 200703 | 2007-03-01 | 961,289.24 | 2,646,673.39 | 2,646,673.39 |
| 200704 | 2007-04-01 | 1,128,104.82 | 1,128,104.82 | 3,774,778.20 |
| … | … | … | … | … |
| 200909 | 2009-09-01 | 709,610.40 | 2,613,714.44 | 6,931,979.13 |
| 200910 | 2009-10-01 | 806,738.22 | 806,738.22 | 7,738,717.35 |
| 200911 | 2009-11-01 | 868,164.01 | 1,674,902.23 | 8,606,881.36 |
| 200912 | 2009-12-01 | 746,933.50 | 2,421,835.73 | 9,353,814.87 |

## Related articles

Learn more about DATESYTD in the following articles:

- [**Time Intelligence in Power BI Desktop**](https://www.sqlbi.com/articles/time-intelligence-in-power-bi-desktop/)

  In Power BI Desktop (as of February 2016) you have to use DAX to apply calculations over dates (such as year-to-date, year-over-year, and others), but you do not have the Mark as Date Table feature. This article describes which scenarios are impacted and the possible workarounds. [» Read more](https://www.sqlbi.com/articles/time-intelligence-in-power-bi-desktop/)
- [**International year\_end\_date for YTD functions in DAX**](https://www.sqlbi.com/blog/marco/2018/04/06/international-year-end-date-for-ytd-functions-in-dax/)

  If you used the DATESYTD and TOTALYTD functions in DAX, you might have noticed that the optional parameter year\_end\_date is a string defining the last day of the year. This article describes what are the formats allowed in that parameter. [» Read more](https://www.sqlbi.com/blog/marco/2018/04/06/international-year-end-date-for-ytd-functions-in-dax/)
- [**Year-to-date filtering weekdays in DAX**](https://www.sqlbi.com/articles/year-to-date-filtering-weekdays-in-dax/)

  Time intelligence functions oftentimes hide an automatic ALL statement meant to make time intelligence calculations easier. This article describes this behavior and what to do in case it ends up breaking your calculation. [» Read more](https://www.sqlbi.com/articles/year-to-date-filtering-weekdays-in-dax/)
- [**Optimizing time intelligence in DirectQuery**](https://www.sqlbi.com/articles/optimizing-time-intelligence-in-directquery/)

  This article describes how to optimize time intelligence calculations with DirectQuery over SQL in Power BI by avoiding time intelligence DAX functions. [» Read more](https://www.sqlbi.com/articles/optimizing-time-intelligence-in-directquery/)
- [**DAX and semantic models announcements at the Fabric Conference 2025**](https://www.sqlbi.com/blog/marco/2025/04/04/dax-and-semantic-models-announcements-at-the-fabric-conference-2025/)

  I usually do not write about announcements and new features until we have had time to try and test them in the real world. However, there are always exceptions, and some of the announcements at the Microsoft Fabric Conference 2025 fall into this category because I have worked with them enough to provide hands-on feedback. In short, these are the topics I am covering in this blog post: Direct Lake and Import mode Calendars in DAX User Defined Functions (UDF) in DAX Direct Lake and Import mode One year ago, after two conferences where the announcement of Direct Lake was… [» Read more](https://www.sqlbi.com/blog/marco/2025/04/04/dax-and-semantic-models-announcements-at-the-fabric-conference-2025/)
- [**Filtering weekdays in DAX**](https://www.sqlbi.com/articles/filtering-weekdays-in-dax/)

  When using time intelligence functions, the automatic REMOVEFILTERS on Date can make maintaining filters on the Date table challenging. This article shows a technique to handle filter-preserving columns in DAX. [» Read more](https://www.sqlbi.com/articles/filtering-weekdays-in-dax/)
- [**Introducing calendar-based time intelligence in DAX**](https://www.sqlbi.com/articles/introducing-calendar-based-time-intelligence-in-dax/)

  This article introduces the new calendar-based time intelligence functions in DAX, available in preview from the September 2025 release of Power BI. [» Read more](https://www.sqlbi.com/articles/introducing-calendar-based-time-intelligence-in-dax/)

## Related functions

Other related functions are:

- [[DATESMTD]]
- [[DATESQTD]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/datesytd-function-dax](https://docs.microsoft.com/en-us/dax/datesytd-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
