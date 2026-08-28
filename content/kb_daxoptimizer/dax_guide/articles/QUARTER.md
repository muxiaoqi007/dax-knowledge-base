---
title: "QUARTER"
function: "quarter"
category: "Date and Time"
url: "https://dax.guide/quarter/"
source: "dax.guide"
重要度:
难度:
---

# QUARTER DAX Function (Date and Time)

Returns a number from 1 (January-March) to 4 (October-December) representing the quarter.

## Syntax

QUARTER ( <Date> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Date |  | A date in datetime format. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

An integer number indicating the number of the quarter.

## Remarks

If the argument is a string, it is translated into a datetime value using the same rules applied by the [[DATEVALUE]] function.

[» 1 related article](#articles)  
[» 3 related functions](#alt)  

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

Learn more about QUARTER in the following articles:

- [**Computing MTD, QTD, YTD in Power BI for the current period**](https://www.sqlbi.com/articles/computing-mtd-qtd-ytd-in-power-bi-for-the-current-period/)

  This article describes how to use the DAX time intelligence calculations applied to the latest period available in the data, also known as the “current” period. [» Read more](https://www.sqlbi.com/articles/computing-mtd-qtd-ytd-in-power-bi-for-the-current-period/)

## Related functions

Other related functions are:

- [[DAY]]
- [[YEAR]]
- [[YEARFRAC]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/quarter-function-dax](https://docs.microsoft.com/en-us/dax/quarter-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
