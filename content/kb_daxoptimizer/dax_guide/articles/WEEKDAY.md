---
title: "WEEKDAY"
function: "weekday"
category: "Date and Time"
url: "https://dax.guide/weekday/"
source: "dax.guide"
重要度:
难度:
---

# WEEKDAY DAX Function (Date and Time)

Returns a number identifying the day of the week of a date. The number is in a range 1-7 or 0-6 according to the choice of the ReturnType parameter.

## Syntax

WEEKDAY ( <Date> [, <ReturnType>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Date |  | A date in datetime format. |
| ReturnType | Optional | A number that determines the return value:   | **ReturnType** | **Return value** | | --- | --- | | 1 or omitted | Numbers 1 (Sunday) through 7 (Saturday). | | 2 | Numbers 1 (Monday) through 7 (Sunday). | | 3 | Numbers 0 (Monday) through 6 (Sunday). | | 11 | Numbers 1 (Monday) through 7 (Sunday). | | 12 | Numbers 1 (Tuesday) through 7 (Monday). | | 13 | Numbers 1 (Wednesday) through 7 (Tuesday). | | 14 | Numbers 1 (Thursday) through 7 (Wednesday). | | 15 | Numbers 1 (Friday) through 7 (Thursday). | | 16 | Numbers 1 (Saturday) through 7 (Friday). | | 17 | Numbers 1 (Sunday) through 7 (Saturday). | |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

An integer number in a range 1-7 or 0-6 according to the choice of the ReturnType parameter.

## Remarks

If the argument is a string, it is translated into a datetime value using the same rules applied by the [[DATEVALUE]] function.

[» 1 related article](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  WEEKDAY returns the day of the week using different standards

EVALUATE

ADDCOLUMNS (

    TOPN ( 10, VALUES ( 'Date'[Date] ), 'Date'[Date], ASC ),

    "Day of week", FORMAT  ( 'Date'[Date], "dddd" ),

    "Weekday",     WEEKDAY ( 'Date'[Date]      ),    -- 1 is Sunday (Default)

    "Weekday 1",   WEEKDAY ( 'Date'[Date], 1   ),    -- 1 is Sunday

    "Weekday 2",   WEEKDAY ( 'Date'[Date], 2   ),    -- 1 is Monday

    "Weekday 3",   WEEKDAY ( 'Date'[Date], 3   ),    -- 0 is Monday

    "Weekday 11",  WEEKDAY ( 'Date'[Date], 11  ),    -- 1 is Monday

    "Weekday 12",  WEEKDAY ( 'Date'[Date], 12  ),    -- 1 is Tuesday

    "Weekday 13",  WEEKDAY ( 'Date'[Date], 13  ),    -- 1 is Wednesday

    "Weekday 14",  WEEKDAY ( 'Date'[Date], 14  )     -- ... 

)

ORDER BY [Date]

```

| Date | Day of week | Weekday | Weekday 1 | Weekday 2 | Weekday 3 | Weekday 11 | Weekday 12 | Weekday 13 | Weekday 14 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 2005-01-01 | Saturday | 7 | 7 | 6 | 5 | 6 | 5 | 4 | 3 |
| 2005-01-02 | Sunday | 1 | 1 | 7 | 6 | 7 | 6 | 5 | 4 |
| 2005-01-03 | Monday | 2 | 2 | 1 | 0 | 1 | 7 | 6 | 5 |
| 2005-01-04 | Tuesday | 3 | 3 | 2 | 1 | 2 | 1 | 7 | 6 |
| 2005-01-05 | Wednesday | 4 | 4 | 3 | 2 | 3 | 2 | 1 | 7 |
| 2005-01-06 | Thursday | 5 | 5 | 4 | 3 | 4 | 3 | 2 | 1 |
| 2005-01-07 | Friday | 6 | 6 | 5 | 4 | 5 | 4 | 3 | 2 |
| 2005-01-08 | Saturday | 7 | 7 | 6 | 5 | 6 | 5 | 4 | 3 |
| 2005-01-09 | Sunday | 1 | 1 | 7 | 6 | 7 | 6 | 5 | 4 |
| 2005-01-10 | Monday | 2 | 2 | 1 | 0 | 1 | 7 | 6 | 5 |

## Related articles

Learn more about WEEKDAY in the following articles:

- [**Counting working days in DAX**](https://www.sqlbi.com/articles/counting-working-days-in-dax/)

  This article shows a DAX technique to compute the number of working days between two dates. [» Read more](https://www.sqlbi.com/articles/counting-working-days-in-dax/)

## Related functions

Other related functions are:

- [[WEEKNUM]]
- [[YEARFRAC]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/weekday-function-dax](https://docs.microsoft.com/en-us/dax/weekday-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
