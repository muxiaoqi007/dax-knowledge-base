---
title: "YEAR"
function: "year"
category: "Date and Time"
url: "https://dax.guide/year/"
source: "dax.guide"
重要度:
难度:
---

# YEAR DAX Function (Date and Time)

Returns the year of a date as a four digit integer.

## Syntax

YEAR ( <Date> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Date |  | A date in datetime format. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

An integer in the range 1900-9999.

## Remarks

If the argument is a string, it is translated into a datetime value using the same rules applied by the [[DATEVALUE]] function.

[» 2 related articles](#articles)  
[» 6 related functions](#alt)  

## Examples

```dax


--  Extract date parts from a date

EVALUATE

ADDCOLUMNS (

    TOPN ( 10, VALUES ( 'Date'[Date] ), 'Date'[Date], ASC ),

    "Year",    YEAR    ( 'Date'[Date] ),     

    "Quarter", QUARTER ( 'Date'[Date] ),

    "Month",   MONTH   ( 'Date'[Date] ),    

    "Day",     DAY     ( 'Date'[Date] )         

)

ORDER BY 'Date'[Date]

```

| Date | Year | Quarter | Month | Day |
| --- | --- | --- | --- | --- |
| 2005-01-01 | 2,005 | 1 | 1 | 1 |
| 2005-01-02 | 2,005 | 1 | 1 | 2 |
| 2005-01-03 | 2,005 | 1 | 1 | 3 |
| 2005-01-04 | 2,005 | 1 | 1 | 4 |
| 2005-01-05 | 2,005 | 1 | 1 | 5 |
| 2005-01-06 | 2,005 | 1 | 1 | 6 |
| 2005-01-07 | 2,005 | 1 | 1 | 7 |
| 2005-01-08 | 2,005 | 1 | 1 | 8 |
| 2005-01-09 | 2,005 | 1 | 1 | 9 |
| 2005-01-10 | 2,005 | 1 | 1 | 10 |

## Related articles

Learn more about YEAR in the following articles:

- [**DAX limitations with inactive relationships and row-level security (RLS)**](https://www.sqlbi.com/articles/dax-limitations-with-inactive-relationships-and-row-level-security-rls/)

  When you apply row-level security to a semantic model, there are limitations in using the USERELATIONSHIP function. This article shows the issues, provides a workaround, and its restrictions. [» Read more](https://www.sqlbi.com/articles/dax-limitations-with-inactive-relationships-and-row-level-security-rls/)
- [**Using weekly calendars in Power BI**](https://www.sqlbi.com/articles/using-weekly-calendars-in-power-bi/)

  This article describes why week-based calendars (like 4-4-5) are important for specific industries and how to use them effectively in Power BI. [» Read more](https://www.sqlbi.com/articles/using-weekly-calendars-in-power-bi/)

## Related functions

Other related functions are:

- [[DAY]]
- [[HOUR]]
- [[MINUTE]]
- [[MONTH]]
- [[SECOND]]
- [[QUARTER]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/year-function-dax](https://docs.microsoft.com/en-us/dax/year-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
