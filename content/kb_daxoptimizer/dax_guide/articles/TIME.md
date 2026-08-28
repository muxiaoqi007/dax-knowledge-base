---
title: "TIME"
function: "time"
category: "Date and Time"
url: "https://dax.guide/time/"
source: "dax.guide"
重要度:
难度:
---

# TIME DAX Function (Date and Time)

Converts hours, minutes, and seconds given as numbers to a time in datetime format.

## Syntax

TIME ( <Hour>, <Minute>, <Second> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Hour |  | A number from 0 to 23 representing the hour. |
| Minute |  | A number from 0 to 59 representing the minute. |
| Second |  | A number from 0 to 59 representing the second. |

## Return values

Scalar A single [datetime](https://dax.guide/dt/datetime/) value.

Returns the specified time.

## Remarks

Each argument can have a value larger than the possible number of hours/minutes/seconds. However, the maximum number accepted in each argument is 32,767. Therefore, you cannot convert any number of seconds in the corresponding time of the day (one day has 86,400 seconds, the maximum value for the Seconds argument is 32,767).

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

## Related functions

Other related functions are:

- [[DATE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Maxim Zelensky, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/time-function-dax](https://docs.microsoft.com/en-us/dax/time-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
