---
title: "EOMONTH"
function: "eomonth"
category: "Date and Time"
url: "https://dax.guide/eomonth/"
source: "dax.guide"
重要度:
难度:
---

# EOMONTH DAX Function (Date and Time)

Returns the date in datetime format of the last day of the month before or after a specified number of months.

## Syntax

EOMONTH ( <StartDate>, <Months> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| StartDate |  | The start date in datetime format. |
| Months |  | The number of months before or after the start\_date. |

## Return values

Scalar A single [datetime](https://dax.guide/dt/datetime/) value.

## Remarks

Because there is no function SOMONTH to get the start of the month, the EOMONTH can be used to get the start of the month by subtracting one more month and adding one to the result, as shown in the examples.

[» 2 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  EDATE adds months to a date, 

--  EOMONTH does the same, and it also goes at the end of the month

EVALUATE

ADDCOLUMNS (

    TOPN ( 10, VALUES ( 'Date'[Date] ), 'Date'[Date], ASC ),

    "EDATE, +1", EDATE ( 'Date'[Date], +1 ),

    "EDATE, -1", EDATE ( 'Date'[Date], -1 ),

    "EOMONTH, 0", EOMONTH ( 'Date'[Date], 0 ),

    "EOMONTH, +1", EOMONTH ( 'Date'[Date], +1 ),

    "EOMONTH, -1", EOMONTH ( 'Date'[Date], -1 )

)

ORDER BY 'Date'[Date]

```

| Date | EDATE, +1 | EDATE, -1 | EOMONTH, 0 | EOMONTH, +1 | EOMONTH, -1 |
| --- | --- | --- | --- | --- | --- |
| 2005-01-01 | 2005-02-01 | 2004-12-01 | 2005-01-31 | 2005-02-28 | 2004-12-31 |
| 2005-01-02 | 2005-02-02 | 2004-12-02 | 2005-01-31 | 2005-02-28 | 2004-12-31 |
| 2005-01-03 | 2005-02-03 | 2004-12-03 | 2005-01-31 | 2005-02-28 | 2004-12-31 |
| 2005-01-04 | 2005-02-04 | 2004-12-04 | 2005-01-31 | 2005-02-28 | 2004-12-31 |
| 2005-01-05 | 2005-02-05 | 2004-12-05 | 2005-01-31 | 2005-02-28 | 2004-12-31 |
| 2005-01-06 | 2005-02-06 | 2004-12-06 | 2005-01-31 | 2005-02-28 | 2004-12-31 |
| 2005-01-07 | 2005-02-07 | 2004-12-07 | 2005-01-31 | 2005-02-28 | 2004-12-31 |
| 2005-01-08 | 2005-02-08 | 2004-12-08 | 2005-01-31 | 2005-02-28 | 2004-12-31 |
| 2005-01-09 | 2005-02-09 | 2004-12-09 | 2005-01-31 | 2005-02-28 | 2004-12-31 |
| 2005-01-10 | 2005-02-10 | 2004-12-10 | 2005-01-31 | 2005-02-28 | 2004-12-31 |

```dax


--  To get the beginning of a month, 

--  subtract one more month and add one to the result

EVALUATE

ADDCOLUMNS (

    TOPN ( 10, VALUES ( 'Date'[Date] ), 'Date'[Date], ASC ),

    "End current month", EOMONTH ( 'Date'[Date], 0 ),

    "End next month", EOMONTH ( 'Date'[Date], +1 ),

    "End prev. month", EOMONTH ( 'Date'[Date], -1 ),

    "Start current month", EOMONTH ( 'Date'[Date], -1 ) + 1,

    "Start next month", EOMONTH ( 'Date'[Date], 0 ) + 1,

    "Start prev. month", EOMONTH ( 'Date'[Date], -2 ) + 1

)

ORDER BY 'Date'[Date]

```

| Date[Date] | End current month | End next month | End prev. month | Start current month | Start next month | Start prev. month |
| --- | --- | --- | --- | --- | --- | --- |
| 2005-01-01 | 2005-01-31 | 2005-02-28 | 2004-12-31 | 2005-01-01 | 2005-02-01 | 2004-12-01 |
| 2005-01-02 | 2005-01-31 | 2005-02-28 | 2004-12-31 | 2005-01-01 | 2005-02-01 | 2004-12-01 |
| 2005-01-03 | 2005-01-31 | 2005-02-28 | 2004-12-31 | 2005-01-01 | 2005-02-01 | 2004-12-01 |
| 2005-01-04 | 2005-01-31 | 2005-02-28 | 2004-12-31 | 2005-01-01 | 2005-02-01 | 2004-12-01 |
| 2005-01-05 | 2005-01-31 | 2005-02-28 | 2004-12-31 | 2005-01-01 | 2005-02-01 | 2004-12-01 |
| 2005-01-06 | 2005-01-31 | 2005-02-28 | 2004-12-31 | 2005-01-01 | 2005-02-01 | 2004-12-01 |
| 2005-01-07 | 2005-01-31 | 2005-02-28 | 2004-12-31 | 2005-01-01 | 2005-02-01 | 2004-12-01 |
| 2005-01-08 | 2005-01-31 | 2005-02-28 | 2004-12-31 | 2005-01-01 | 2005-02-01 | 2004-12-01 |
| 2005-01-09 | 2005-01-31 | 2005-02-28 | 2004-12-31 | 2005-01-01 | 2005-02-01 | 2004-12-01 |
| 2005-01-10 | 2005-01-31 | 2005-02-28 | 2004-12-31 | 2005-01-01 | 2005-02-01 | 2004-12-01 |

## Related articles

Learn more about EOMONTH in the following articles:

- [**Improving timeline charts in Power BI with DAX**](https://www.sqlbi.com/articles/improving-temporal-line-charts-in-power-bi-with-dax/)

  This article shows how to improve line charts with a date-based X-Axis in Power BI using DAX, and how to make correct choices in the data modeling and visualization properties. [» Read more](https://www.sqlbi.com/articles/improving-temporal-line-charts-in-power-bi-with-dax/)
- [**Optimizing time intelligence in DirectQuery**](https://www.sqlbi.com/articles/optimizing-time-intelligence-in-directquery/)

  This article describes how to optimize time intelligence calculations with DirectQuery over SQL in Power BI by avoiding time intelligence DAX functions. [» Read more](https://www.sqlbi.com/articles/optimizing-time-intelligence-in-directquery/)

## Related functions

Other related functions are:

- [[EDATE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/eomonth-function-dax](https://docs.microsoft.com/en-us/dax/eomonth-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
