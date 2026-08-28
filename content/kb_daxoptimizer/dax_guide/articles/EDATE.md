---
title: "EDATE"
function: "edate"
category: "Date and Time"
url: "https://dax.guide/edate/"
source: "dax.guide"
重要度:
难度:
---

# EDATE DAX Function (Date and Time)

Returns the date that is the indicated number of months before or after the start date.

## Syntax

EDATE ( <StartDate>, <Months> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| StartDate |  | A date in datetime format that represents the start date. |
| Months |  | The number of months before or after start\_date. |

## Return values

Scalar A single [datetime](https://dax.guide/dt/datetime/) value.

[» 1 related article](#articles)  
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

## Related articles

Learn more about EDATE in the following articles:

- [**Previous year up to a certain date**](https://www.sqlbi.com/articles/previous-year-up-to-a-certain-date/)

  This article describes how to compute previous year values up to a certain date. This is useful in case the data is presenting incomplete months or years. [» Read more](https://www.sqlbi.com/articles/previous-year-up-to-a-certain-date/)

## Related functions

Other related functions are:

- [[EOMONTH]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/edate-function-dax](https://docs.microsoft.com/en-us/dax/edate-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
