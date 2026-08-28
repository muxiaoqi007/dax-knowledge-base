---
title: "UTCTODAY"
function: "utctoday"
category: "Date and Time"
url: "https://dax.guide/utctoday/"
source: "dax.guide"
重要度:
难度:
---

# UTCTODAY DAX Function (Date and Time) Volatile

Returns the current date in datetime format expressed in Coordinated Universal Time (UTC).

## Syntax

UTCTODAY ( )

This expression has no parameters.

## Return values

Scalar A single [datetime](https://dax.guide/dt/datetime/) value.

Current UTC date.

## Remarks

The time returned is always 12:00:00 AM and only the date is updated.

The result of the UTCTODAY function changes only when the column that contains the formula is refreshed. It is not updated continuously.

The [[UTCNOW]] function returns also the current time.

[» 3 related functions](#alt)  

## Examples

```dax


--  TODAY returns today, as a date. NOW also includes the time

--  UTCTODAY and UTCNOW return today using UTC standard.

--  The timezone is the timezone of the server running DAX, your

--  PC when executed in Power BI Desktop.

--

--  The Power BI Service alwyas uses UTC. 

--  Therefore, no daylight saving applies.

--  

--  Keep in mind that DAX.do caches query results, so you will not see

--  an updated result if you try this query without making any change.

EVALUATE

{ 

    ( "TODAY", TODAY () ),

    ( "UTCTODAY", UTCTODAY () ),

    ( "NOW", NOW () ),

    ( "UTC NOW", UTCNOW () )

}

```

| Value1 | Value2 |
| --- | --- |
| TODAY | 2021-02-26 00:00:00 |
| UTCTODAY | 2021-02-26 00:00:00 |
| NOW | 2021-02-26 10:31:35.75 |
| UTC NOW | 2021-02-26 10:31:35.75 |

```dax


--  Compute the difference in days and hours between 

--  current time zone and UTC.

EVALUATE

VAR DaysFromUTC = INT ( TODAY () - UTCTODAY () )

VAR HoursFromUTC = ( NOW () - UTCNOW () ) * 24

RETURN

{ 

    ( "Days from UTC: ",  DaysFromUTC  ),

    ( "Hours from UTC: ",  HoursFromUTC)

}

```

```dax


--  Example of using math over dates to compute 

--  the age of the customers

--  by subtracting from TODAY the order date

--

--  Keep in mind that DAX.do caches query results, so you will not see

--  an updated result if you try this query without making any change.

EVALUATE

ADDCOLUMNS ( 

    TOPN ( 10, ALL ( Customer[Name], Customer[Birth Date] ) ),

    "Customer Age", 

    VAR Age = TODAY () - Customer[Birth Date]

    VAR AgeYears = YEAR ( Age ) - 1900

    VAR AgeMonths = MONTH ( Age )

    RETURN

        FORMAT ( AgeYears, "0" ) & " years and " & FORMAT ( AgeMonths, "0" ) & " months" 

    )

ORDER BY Customer[Birth Date] DESC

```

| Name | Birth Date | Customer Age |
| --- | --- | --- |
| Anderson, Chloe | 1979-10-25 | 41 years and 5 months |
| Russell, Jennifer | 1978-12-18 | 42 years and 3 months |
| Xie, Russell | 1978-09-17 | 42 years and 6 months |
| Morris, Isabella | 1978-09-07 | 42 years and 6 months |
| Carter, Amanda | 1977-10-16 | 43 years and 5 months |
| Alexander, Seth | 1977-09-21 | 43 years and 6 months |
| Lopez, Sophia | 1977-07-13 | 43 years and 8 months |
| Simmons, Nathan | 1976-02-24 | 45 years and 1 months |
| Garcia, Joseph | 1975-08-17 | 45 years and 7 months |
| Green, Gabriel | 1975-04-05 | 45 years and 11 months |

## Related functions

Other related functions are:

- [[NOW]]
- [[TODAY]]
- [[UTCNOW]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/utctoday-function-dax](https://docs.microsoft.com/en-us/dax/utctoday-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
