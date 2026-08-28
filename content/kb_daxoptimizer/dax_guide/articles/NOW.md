---
title: "NOW"
function: "now"
category: "Date and Time"
url: "https://dax.guide/now/"
source: "dax.guide"
重要度:
难度:
---

# NOW DAX Function (Date and Time) Volatile

Returns the current date and time in datetime format.

## Syntax

NOW ( )

This expression has no parameters.

## Return values

Scalar A single [datetime](https://dax.guide/dt/datetime/) value.

Current date and time.

## Remarks

The result of the NOW function changes only when the column that contains the formula is refreshed. It is not updated continuously.  
The [[TODAY]] function returns the same date but is not precise with regard to time; the time returned is always 12:00:00 AM and only the date is updated.

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

--  

--  Keep in mind that DAX.do caches query results, so you will not see

--  an updated result if you try this query without making any change.

EVALUATE

VAR DaysFromUTC = INT ( TODAY () - UTCTODAY () )

VAR HoursFromUTC = ( NOW () - UTCNOW () ) * 24

RETURN

{ 

    ( "Days from UTC: ",  DaysFromUTC  ),

    ( "Hours from UTC: ",  HoursFromUTC)

}

```

## Related functions

Other related functions are:

- [[TODAY]]
- [[UTCNOW]]
- [[UTCTODAY]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/now-function-dax](https://docs.microsoft.com/en-us/dax/now-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
