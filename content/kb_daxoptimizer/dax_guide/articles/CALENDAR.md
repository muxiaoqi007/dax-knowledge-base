---
title: "CALENDAR"
function: "calendar"
category: "Date and Time"
url: "https://dax.guide/calendar/"
source: "dax.guide"
重要度:
难度:
---

# CALENDAR DAX Function (Date and Time)

Returns a table with one column of all dates between StartDate and EndDate.

## Syntax

CALENDAR ( <StartDate>, <EndDate> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| StartDate |  | The start date in datetime format. |
| EndDate |  | The end date in datetime format. |

## Return values

Table A table with a single column.

Returns a table with a single column named “Date” containing a contiguous set of dates. The range of dates is from the specified start date to the specified end date, inclusive of those two dates.

## Remarks

The CALENDAR table is useful to create a Date table.  
For compatibility with DAX time intelligence functions, it is a best practice to always include an entire year in a Date table.

[» 2 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

The following formula returns a table with dates between January 1st, 2005 and December 31st, 2015.

```dax


CALENDAR (

    DATE ( 2005, 1, 1 ), 

    DATE ( 2015, 12, 31 )

)

```

The following expression returns the date table covering the range of dates in actual sales data and future sales forecasts.

```dax


CALENDAR (

    DATE ( YEAR ( MIN ( Sales[Date] ) ), 1, 1 ),

    DATE ( YEAR ( MAX ( Forecast[Date] ) ), 12, 31 )

)

```

Because a date column can be represented as an integer, CALENDAR can be used as a way to obtain a result similar to a particular call to GENERATESERIES. For example, the following expressions are equivalent, even if the former produces a table with a DateTime data type, whereas the latter returns a table with an Integer data type.

```dax


CALENDAR ( 1, 100 )



GENERATESERIES ( 1, 100, 1 )

```

```dax


-- CALENDAR requires to specify the boundaries, it returns a table with

-- one column (Date) including all the dates between the boundaries (included)

EVALUATE 

    CALENDAR (

        DATE ( 2022, 11, 26 ),

        DATE ( 2022, 12, 5 )

    )

ORDER BY [Date] DESC

```

| Date |
| --- |
| 2022-12-05 |
| 2022-12-04 |
| 2022-12-03 |
| 2022-12-02 |
| 2022-12-01 |
| 2022-11-30 |
| 2022-11-29 |
| 2022-11-28 |
| 2022-11-27 |
| 2022-11-26 |

## Related articles

Learn more about CALENDAR in the following articles:

- [**Reference Date Table in DAX and Power BI**](https://www.sqlbi.com/articles/reference-date-table-in-dax-and-power-bi/)

  This article describes a reference Date table in DAX using a Power BI template. The same technique can be used in Analysis Services models. Download the latest version of the template in the Dax Date Template page. [» Read more](https://www.sqlbi.com/articles/reference-date-table-in-dax-and-power-bi/)
- [**Creating a simple date table in DAX**](https://www.sqlbi.com/articles/creating-a-simple-date-table-in-dax/)

  This article shows how to build a basic date table using a calculated table and DAX. [» Read more](https://www.sqlbi.com/articles/creating-a-simple-date-table-in-dax/)

## Related functions

Other related functions are:

- [[CALENDARAUTO]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/calendar-function-dax](https://docs.microsoft.com/en-us/dax/calendar-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
