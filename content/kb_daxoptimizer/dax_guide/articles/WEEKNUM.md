---
title: "WEEKNUM"
function: "weeknum"
category: "Date and Time"
url: "https://dax.guide/weeknum/"
source: "dax.guide"
重要度:
难度:
---

# WEEKNUM DAX Function (Date and Time)

Returns the week number in the year.

## Syntax

WEEKNUM ( <Date> [, <ReturnType>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Date |  | A date in datetime format. |
| ReturnType | Optional | A number that determines the return value: for example, use 1 when week begins on Sunday, or use 2 when week begins on Monday, or use 21 for ISO week numbers. More details in Remarks section. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

The week number for the given date.

## Remarks

If the argument is a string, it is translated into a datetime value using the same rules applied by the [[DATEVALUE]] function.

There are two systems used for this function:

- **System 1** The week containing January 1 is the first week of the year, and is numbered week 1.
- **System 2** The week containing the first Thursday of the year is the first week of the year, and is numbered as week 1. This system is the methodology specified in ISO 8601, which is commonly known as the European week numbering system.

| Return\_type | Week begins on | System |
| --- | --- | --- |
| 1 or omitted | Sunday | 1 |
| 2 | Monday | 1 |
| 11 | Monday | 1 |
| 12 | Tuesday | 1 |
| 13 | Wednesday | 1 |
| 14 | Thursday | 1 |
| 15 | Friday | 1 |
| 16 | Saturday | 1 |
| 17 | Sunday | 1 |
| 21 | Monday | 2 |

[» 2 related functions](#alt)  

## Examples

```dax


--

--    WEEKNUM returns the week number following different standards

--

EVALUATE 

ADDCOLUMNS ( 

    TOPN ( 10, VALUES ( 'Date'[Date] ), 'Date'[Date], ASC ),

    "Day of week", FORMAT ( 'Date'[Date], "dddd" ),

    "WEEKNUM",    WEEKNUM ( 'Date'[Date] ) ,    -- Same as 1, week start on Sun

    "WEEKNUM  2", WEEKNUM ( 'Date'[Date],  2 ), -- Week start on Mon

    "WEEKNUM 11", WEEKNUM ( 'Date'[Date], 11 ), -- 11 to 17: 1st DOW Mon 

    "WEEKNUM 12", WEEKNUM ( 'Date'[Date], 12 ), -- 11 to 17: 1st DOW Tue

    "WEEKNUM 13", WEEKNUM ( 'Date'[Date], 13 ), -- 11 to 17: 1st DOW Wed

    "WEEKNUM 21", WEEKNUM ( 'Date'[Date], 21 )  -- ISO 8601 Week 

                                                -- (1st thursday of the year)

)

ORDER BY 'Date'[Date]

```

| Date | Day of week | WEEKNUM | WEEKNUM 2 | WEEKNUM 11 | WEEKNUM 12 | WEEKNUM 13 | WEEKNUM 21 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 2005-01-01 | Saturday | 1 | 1 | 1 | 1 | 1 | 53 |
| 2005-01-02 | Sunday | 2 | 1 | 1 | 1 | 1 | 53 |
| 2005-01-03 | Monday | 2 | 2 | 2 | 1 | 1 | 1 |
| 2005-01-04 | Tuesday | 2 | 2 | 2 | 2 | 1 | 1 |
| 2005-01-05 | Wednesday | 2 | 2 | 2 | 2 | 2 | 1 |
| 2005-01-06 | Thursday | 2 | 2 | 2 | 2 | 2 | 1 |
| 2005-01-07 | Friday | 2 | 2 | 2 | 2 | 2 | 1 |
| 2005-01-08 | Saturday | 2 | 2 | 2 | 2 | 2 | 1 |
| 2005-01-09 | Sunday | 3 | 2 | 2 | 2 | 2 | 1 |
| 2005-01-10 | Monday | 3 | 3 | 3 | 2 | 2 | 2 |

## Related functions

Other related functions are:

- [[WEEKDAY]]
- [[YEARFRAC]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/weeknum-function-dax](https://docs.microsoft.com/en-us/dax/weeknum-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
