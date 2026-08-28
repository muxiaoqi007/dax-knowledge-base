---
title: "DATE"
function: "date"
category: "Date and Time"
url: "https://dax.guide/date/"
source: "dax.guide"
重要度:
难度:
---

# DATE DAX Function (Date and Time)

Returns the specified date in datetime format.

## Syntax

DATE ( <Year>, <Month>, <Day> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Year |  | A four digit number representing the year. |
| Month |  | A number from 1 to 12 representing the month of the year. |
| Day |  | A number from 1 to 31 representing the day of the month. |

## Return values

Scalar A single [datetime](https://dax.guide/dt/datetime/) value.

Returns the specified date.

## Remarks

A DateTime value can be specified as a literal by using the prefix dt before a string with one of the following formats:

- dt”YYYY-MM-DD”
- dt”YYYY-MM-DDThh:mm:ss”
- dt”YYYY-MM-DD hh:mm:ss”

For example, DATE ( 2023, 5, 19 ) corresponds to dt”2023-05-19″.

Notes about the arguments:

- If **Year** is between -1800 (zero) and 99 (inclusive), DATE adds that value to 1900 to calculate the year. For example, DATE(98,1,2) returns January 2, 1998 (1900+98). DATE(-50,1,2) returns January 2, 1850.
- If **Year** is between 1900 and 9999 (inclusive), DATE uses that value as the year. For example, DATE(2008,1,2) returns January 2, 2008.
- If **Year** is less than -1800 and -1 (inclusive), DATE uses that value as the year. For example, DATE(2008,1,2) returns January 2, 2008.
- If **Year** is less than -1800 or is 10000 or greater, DATE raises an error.
- If **Month** is greater than 12, Month adds that number of months to the first month in the year specified. For example, DATE(2008,14,2) returns February 2, 2009.
- If **Month** is less than 1, Month subtracts the magnitude of that number of months, plus 1, from the first month in the year specified. For example, DATE(2008,-3,2) returns the serial number representing September 2, 2007.
- If **Day** is greater than the number of days in the month specified, Day adds that number of days to the first day in the month. For example, DATE(2008,1,35) returns the serial number representing February 4, 2008.
- If **Day** is less than 1, Day subtracts the magnitude of that number of days, plus one, from the first day of the month specified. For example, DATE(2008,1,-15) returns the serial number representing December 16, 2007.

[» 1 related article](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  DATE and TIME are useful to create DateTime columns

--  A DateTime is a number. Therefore, it is possible to

--  sum the date and the time part.

EVALUATE 

    {

        DATE ( 2020, 10, 15 ),

        TIME ( 22, 45, 30 ),

        DATE ( 2020, 10, 15 ) + TIME ( 22, 45, 30 )

    }

```

| Value |
| --- |
| 2020-10-15 00:00:00 |
| 1899-12-30 22:45:30 |
| 2020-10-15 22:45:30 |

```dax


--  When the arguments overflow their range, DATE and TIME

--  behave in different ways:

--

--  DATE adds the excess values to the date, moving time forward

--  TIME adds the excess values, but it does never exceed the day

EVALUATE 

    {

        DATE ( 2020, 10, 32 ),

        DATE ( 2020, 20, 1 ),

        TIME ( 10, 90, 0 ),

        TIME ( 50, 0, 0 )

    }

```

| Value |
| --- |
| 2020-11-01 00:00:00 |
| 2021-08-01 00:00:00 |
| 1899-12-30 11:30:00 |
| 1899-12-30 02:00:00 |

```dax


--  You can use DATE to create a new date, one year later

--  Using built-in functions like EDATE is better, it they are available

EVALUATE 

ADDCOLUMNS(

    TOPN ( 5, VALUES ( 'Date'[Date] ), 'Date'[Date], ASC ),

    "One year later",

        DATE ( 

            YEAR ( 'Date'[Date] ) + 1,

            MONTH ( 'Date'[Date] ),

            DAY ( 'Date'[Date] )

        ),

    "One year later (Using EDATE)",

        EDATE ( 'Date'[Date], 12 )

)

```

| Date | One year later | One year later (Using EDATE) |
| --- | --- | --- |
| 2005-01-05 | 2006-01-05 | 2006-01-05 |
| 2005-01-04 | 2006-01-04 | 2006-01-04 |
| 2005-01-02 | 2006-01-02 | 2006-01-02 |
| 2005-01-01 | 2006-01-01 | 2006-01-01 |
| 2005-01-03 | 2006-01-03 | 2006-01-03 |

## Related articles

Learn more about DATE in the following articles:

- [**Comparing cumulative values for events in different periods**](https://www.sqlbi.com/articles/comparing-cumulative-values-for-events-in-different-periods/)

  This article describes how to compare time series that occur in different periods by standardizing the timelines to days since a specific event. [» Read more](https://www.sqlbi.com/articles/comparing-cumulative-values-for-events-in-different-periods/)

## Related functions

Other related functions are:

- [[TIME]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Philippe Le Page

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/date-function-dax](https://docs.microsoft.com/en-us/dax/date-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
