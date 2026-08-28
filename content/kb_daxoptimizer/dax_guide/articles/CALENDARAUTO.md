---
title: "CALENDARAUTO"
function: "calendarauto"
category: "Date and Time"
url: "https://dax.guide/calendarauto/"
source: "dax.guide"
重要度:
难度:
---

# CALENDARAUTO DAX Function (Date and Time)

Returns a table with one column of dates calculated from the model automatically.

## Syntax

CALENDARAUTO ( [<FiscalYearEndMonth>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| FiscalYearEndMonth | Optional | An integer from 1 to 12 representing the end month of fiscal year. |

## Return values

Table A table with a single column.

Returns a table with a single column named “Date” containing a contiguous set of dates. The range of dates is calculated automatically based on data in the model.

## Remarks

By default, the fiscal year ends in December (month 12).  
CALENDARAUTO ignores calculated tables and calculated columns searching for date columns. Only the imported columns are analyzed to search for date columns.

Internally, CALENDARAUTO calls [[CALENDAR]] providing a date range that includes all the days in the range of years referenced by data in the model, according to the following rules:

- The earliest date in the model which is not in a calculated column or calculated table is taken as the MinDate.
- The latest date in the model which is not in a calculated column or calculated table is taken as the MaxDate.
- The date range returned is dates between the beginning of the fiscal year associated with MinDate and the end of the fiscal year associated with MaxDate.

An error is returned if the model does not contain any datetime values which are not in calculated columns or calculated tables.

[» 2 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

In this example, the MinDate and MaxDate in the data model are July 1, 2010 and June 30, 2019.

CALENDARAUTO ( ) will return all dates between January 1, 2010 and December 31, 2019.

CALENDARAUTO ( 3 ) will return all dates between April 1, 2010 and March 31, 2020.

The following expression will return all dates between January 1, 2015 and March 31, 2020.

```dax


FILTER ( 

    CALENDARAUTO ( 3 ),

    YEAR ( [Date] ) >= 2015

)

```

The following expression creates a simple calculated date table.

```dax


ADDCOLUMNS (

    CALENDARAUTO (),

    "Year", YEAR ( [Date] ),

    "Quarter", "Q" & QUARTER ( [Date] ),

    "Month", FORMAT ( [Date], "mmmm" ),

    "Month Number", MONTH ( [Date] )

)

ORDER BY [Date] ASC

```

## Related articles

Learn more about CALENDARAUTO in the following articles:

- [**Creating a simple date table in DAX**](https://www.sqlbi.com/articles/creating-a-simple-date-table-in-dax/)

  This article shows how to build a basic date table using a calculated table and DAX. [» Read more](https://www.sqlbi.com/articles/creating-a-simple-date-table-in-dax/)
- [**Creating a simpler and chart-friendly Date table in Power BI**](https://www.sqlbi.com/articles/creating-a-simpler-and-chart-friendly-date-table-in-power-bi/)

  A Date table in Power BI can have a smaller number of columns by leveraging custom format strings to adequately control the chart visualization and the sort order. [» Read more](https://www.sqlbi.com/articles/creating-a-simpler-and-chart-friendly-date-table-in-power-bi/)

## Related functions

Other related functions are:

- [[CALENDAR]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/calendarauto-function-dax](https://docs.microsoft.com/en-us/dax/calendarauto-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
