---
title: "TOTALYTD"
function: "totalytd"
category: "Time Intelligence"
url: "https://dax.guide/totalytd/"
source: "dax.guide"
重要度:
难度:
---

# TOTALYTD DAX Function (Time Intelligence) Context Transition

Evaluates the specified expression over the interval which begins on the first day of the year and ends with the last date in the specified date column (or in the evaluation context if a calendar reference is provided) after applying specified filters.

## Syntax

TOTALYTD ( <Expression>, <Dates> [, <Filter>] [, <YearEndDate>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Expression  By Expression |  | The expression to be evaluated. |
| Dates |  | The name of a column containing dates, a single column table containing dates, or a calendar reference. |
| Filter | Optional | A boolean (True/False) expression or a table expression that defines a filter. |
| YearEndDate | Optional | End of year date. |

## Return values

Scalar A single value of any type.

A scalar value that represents the Expression evaluated for the Dates in the current year-to-date, given the dates in Dates.

## Notes

In order to use any time intelligence calculation, you need a well-formed date table. The *Date* table must satisfy the following requirements:

- All dates need to be present for the years required. The *Date* table must always start on January 1 and end on December 31, including all the days in this range. If the report only references fiscal years, then the date table must include all the dates from the first to the last day of a fiscal year. For example, if the fiscal year 2008 starts on July 1, 2007, then the *Date* table must include all the days from July 1, 2007 to June 30, 2008.
- There needs to be a column with a *DateTime* or *Date* data type containing unique values. This column is usually called *Date*. Even though the *Date* column is often used to define relationships with other tables, this is not required. Still, the *Date* column must contain unique values and should be referenced by the Mark as Date Table feature. In case the column also contains a time part, no time should be used – for example, the time should always be 12:00 am.
- The *Date* table must be marked as a date table in the model, in case the relationship between the *Date* table and any other table is not based on the *Date*.

The result of time intelligence functions has the same data lineage as the date column or table provided as an argument.

## Remarks

The Dates argument can be any of the following:

- A reference to a date/time column. Only in this case a context transition applies because the column reference is replaced by
  - [[CALCULATETABLE]] ( [[DISTINCT]] ( <Dates> ) )
- A table expression that returns a single column of date/time values.
- A Boolean expression that defines a single-column table of date/time values.

The syntax:

```dax


TOTALYTD ( 

    <Expression>, 

    <Dates> 

    [, <Filter>] 

    [, <YearEndDate>]

) 

```

corresponds to:

```dax


CALCULATE ( 

    <Expression>, 

    DATESYTD ( <Dates> [, <YearEndDate>] )

    [, <Filter>]

)

```

[» 3 related articles](#articles)  
[» 5 related functions](#alt)  

## Examples

```dax


--  TOTALYTD is just syntax sugar for CALCULATE / DATESYTD

EVALUATE

CALCULATETABLE (

    { (

        CALCULATE (            

            [Sales Amount],

            DATESYTD ( 'Date'[Date] )  -- 2007-01-01 : 2007-05-12

        ),

        TOTALYTD (            -- 2007-01-01 : 2007-05-12

            [Sales Amount],

            'Date'[Date]       

        )

    ) },

    'Date'[Date] = DATE ( 2007, 5, 12 )

)



```

| Value1 | Value2 |
| --- | --- |
| 4,038,742.76 | 4,038,742.76 |

## Related articles

Learn more about TOTALYTD in the following articles:

- [**Time Intelligence in Power BI Desktop**](https://www.sqlbi.com/articles/time-intelligence-in-power-bi-desktop/)

  In Power BI Desktop (as of February 2016) you have to use DAX to apply calculations over dates (such as year-to-date, year-over-year, and others), but you do not have the Mark as Date Table feature. This article describes which scenarios are impacted and the possible workarounds. [» Read more](https://www.sqlbi.com/articles/time-intelligence-in-power-bi-desktop/)
- [**Week-Based Time Intelligence in DAX**](https://www.sqlbi.com/articles/week-based-time-intelligence-in-dax/)

  The DAX language provides several Time Intelligence functions that simplify writing calculations such as year-to-date (YTD), year-over-year (YOY) and so on. However, if you have a special calendar structure such as a 4-4-5 weeks’ calendar, you need to write your custom Time Intelligence calculation. In this article, you will learn how to write the required […] [» Read more](https://www.sqlbi.com/articles/week-based-time-intelligence-in-dax/)
- [**Time Patterns**](https://www.daxpatterns.com/time-patterns/)

  The DAX time patterns are used to implement time-related calculations without relying on DAX time intelligence functions. This is useful whenever you have custom calendars, such as an ISO 8601 week calendar, or when you are using an Analysis Services Tabular model in DirectQuery mode. [» Read more](https://www.daxpatterns.com/time-patterns/)

## Related functions

Other related functions are:

- [[DATESMTD]]
- [[DATESQTD]]
- [[DATESYTD]]
- [[TOTALMTD]]
- [[TOTALQTD]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/totalytd-function-dax](https://docs.microsoft.com/en-us/dax/totalytd-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
