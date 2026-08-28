---
title: "GENERATESERIES"
function: "generateseries"
category: "Table manipulation"
url: "https://dax.guide/generateseries/"
source: "dax.guide"
重要度:
难度:
---

# GENERATESERIES DAX Function (Table manipulation)

Returns a table with one column, populated with sequential values from start to end.

## Syntax

GENERATESERIES ( <StartValue>, <EndValue> [, <IncrementValue>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| StartValue |  | The start value of the column. |
| EndValue |  | The end value of the column. |
| IncrementValue | Optional | The increment value of the column. |

## Return values

Table A table with a single column.

A single column table containing the values of an arithmetic series.  
The name of the column is Value.

## Remarks

When EndValue is less than StartValue, an empty table is returned.  
IncrementValue must be a positive value.  
The sequence stops at the last value that is less than or equal to EndValue.

Pay attention to using GENERATESERIES to generate sequences of floating-point numbers or time series: because time series are internally stored as floating-point numbers, the value is represented with an approximation, and trying to get an exact match could result in a failed match for two apparently identical time values. It is always better to compare a time series (like hours/minutes/seconds) using a whole number (like seconds in the day) rather than using the DATETIME data type, which represents the time of day as a fraction of the day as a floating-point number.

[» 3 related articles](#articles)  

## Examples

```dax


--  GENERATESERIES returns a list of values given the first

--  value, the last and the step.

EVALUATE 

    GENERATESERIES ( 1, 10, 1 )

    

EVALUATE 

    GENERATESERIES ( 5, 20, 5 )

    

EVALUATE 

    GENERATESERIES ( 0, 1, 0.1 )

```

| Value |
| --- |
| 1 |
| 2 |
| 3 |
| 4 |
| 5 |
| 6 |
| 7 |
| 8 |
| 9 |
| 10 |

| Value |
| --- |
| 5 |
| 10 |
| 15 |
| 20 |

| Value |
| --- |
| 0.00 |
| 0.10 |
| 0.20 |
| 0.30 |
| 0.40 |
| 0.50 |
| 0.60 |
| 0.70 |
| 0.80 |
| 0.90 |
| 1.00 |

```dax


--  GENERATESERIES works with date and time too, 

--  because dates are numbers.

EVALUATE 

    GENERATESERIES ( 

        DATE ( 2021, 1, 28 ),

        DATE ( 2021, 2, 3 ),

        1

    )

ORDER BY [Value]



EVALUATE

SELECTCOLUMNS (

    GENERATESERIES (

        TIME ( 0, 0, 0 ),

        TIME ( 23, 59, 59 ),

        TIME ( 2, 30, 0 )

    ),

    "Time", FORMAT ( [Value], "hh:mm:ss" )

)

```

| Value |
| --- |
| 2021-01-28 |
| 2021-01-29 |
| 2021-01-30 |
| 2021-01-31 |
| 2021-02-01 |
| 2021-02-02 |
| 2021-02-03 |

| Time |
| --- |
| 00:00:00 |
| 02:30:00 |
| 05:00:00 |
| 07:30:00 |
| 10:00:00 |
| 12:30:00 |
| 15:00:00 |
| 17:30:00 |
| 20:00:00 |
| 22:30:00 |

This example uses GENERATE to create a table for time within a day with the minute granularity.

```dax


EVALUATE

//Time Dimension 

//Working Hours 9:00am to 5:00pm 

VAR Opening = 9

VAR Closing = 17

//Time Slot 

VAR TimeSlot = 30

VAR Offset = ( TimeSlot / 2 )

RETURN

    GENERATE (

        GENERATESERIES ( 0, 1439, 1 ),

        // 24 hours/day * 60 minutes/hour = 1,440 - 1 (initial time 12am) 

        VAR __Minutes = [Value]

        VAR __Time =

            TIME ( 0, __Minutes, 0 )

        // MROUND: Rounds Value to the nearest multiple of TimeSlot

        VAR __MinutesRounded =

            MROUND ( ( __Minutes + Offset ), TimeSlot )

        VAR __StartTimeSlotRounded =

            TIME ( 0, __MinutesRounded - TimeSlot, 0 )

        VAR __EndTimeSlotRounded =

            TIME ( 0, __MinutesRounded, 0 )

        RETURN

            ROW (

                    "Hour", HOUR ( __Time ),

                    "Minute", MINUTE ( __Time ),

                    "Hour Minute 24h", FORMAT ( __Time, "hh:mm" ),

                    "Hour Minute 12h", FORMAT ( __Time, "hh:mm AM/PM" ),

                    "30 Minute Slot Start", FORMAT ( __StartTimeSlotRounded, "hh:mm" ),

                    "30 Minute Slot End", FORMAT ( __EndTimeSlotRounded, "hh:mm" ),

                    "30 Minute Slot", FORMAT ( __StartTimeSlotRounded, "hh:mm" ) & "-"

                        & FORMAT ( __EndTimeSlotRounded, "hh:mm" ),

                    "Working Hours", IF (

                        ( __Minutes / 60 ) >= Opening

                            && ( __Minutes / 60 ) <= Closing,

                        "Yes",

                        "No"

                    ),

                    "Time", __Time,

                    "TimeSlot Start", __StartTimeSlotRounded,

                    "TimeSlot End", __EndTimeSlotRounded

            )

    )

```

| Value | Hour | Minute | Hour Minute 24h | Hour Minute 12h | 30 Minute Slot Start | 30 Minute Slot End | 30 Minute Slot | Working Hours | Time | TimeSlot Start | TimeSlot End |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 0 | 0 | 0 | 00:00 | 12:00 AM | 00:00 | 00:30 | 00:00-00:30 | No | 00:00:00 | 00:00:00 | 00:30:00 |
| 1 | 0 | 1 | 00:01 | 12:01 AM | 00:00 | 00:30 | 00:00-00:30 | No | 00:01:00 | 00:00:00 | 00:30:00 |
| 2 | 0 | 2 | 00:02 | 12:02 AM | 00:00 | 00:30 | 00:00-00:30 | No | 00:02:00 | 00:00:00 | 00:30:00 |
| 3 | 0 | 3 | 00:03 | 12:03 AM | 00:00 | 00:30 | 00:00-00:30 | No | 00:03:00 | 00:00:00 | 00:30:00 |
| 4 | 0 | 4 | 00:04 | 12:04 AM | 00:00 | 00:30 | 00:00-00:30 | No | 00:04:00 | 00:00:00 | 00:30:00 |
| 5 | 0 | 5 | 00:05 | 12:05 AM | 00:00 | 00:30 | 00:00-00:30 | No | 00:05:00 | 00:00:00 | 00:30:00 |
| 6 | 0 | 6 | 00:06 | 12:06 AM | 00:00 | 00:30 | 00:00-00:30 | No | 00:06:00 | 00:00:00 | 00:30:00 |
| … | … | … | … | … | … | … | … | … | … | … | … |
| 1,438 | 23 | 58 | 23:58 | 11:58 PM | 23:30 | 00:00 | 23:30-00:00 | No | 23:58:00 | 23:30:00 | 00:00:00 |
| 1,439 | 23 | 59 | 23:59 | 11:59 PM | 23:30 | 00:00 | 23:30-00:00 | No | 23:59:00 | 23:30:00 | 00:00:00 |

```dax


-- Covert a list of items in a string

-- into a table with one row for each item

--

--  PATHLENGTH determines the number of items

--  GENERATESERIES iterates the items

--  PATHITEM extract the Nth item

EVALUATE

VAR list = "123|456|789|764"

VAR _length =

    PATHLENGTH ( list )

VAR Result =

    SELECTCOLUMNS (

        GENERATESERIES ( 1, _length ),

        -- Use TEXT instead of INTEGER 

        -- to get a list of strings

        "Item", PATHITEM ( list, [value], INTEGER )

    )

RETURN

    Result



```

| Item |
| --- |
| 123 |
| 456 |
| 789 |
| 764 |

## Related articles

Learn more about GENERATESERIES in the following articles:

- [**Generating a series of numbers in DAX**](https://www.sqlbi.com/articles/generating-a-series-of-numbers-in-dax/)

  This article describes how to create a table with a series of numbers in DAX by using the new GENERATESERIES function or through a workaround using CALENDAR. [» Read more](https://www.sqlbi.com/articles/generating-a-series-of-numbers-in-dax/)
- [**Strings list to table in DAX**](https://www.sqlbi.com/blog/marco/2024/05/18/strings-list-to-table-in-dax/)

  DAX is not like M when it comes to data manipulation, and it is not supposed to do that. However, if you need something in DAX similar to Table.FromList in M, this blog post is for you. If you have a list of values in a string in DAX and you want to obtain a table with one row for each item in the list, you can do the following: Use “|” as item separator in the string (instead of “,” or “;”) Determines the number of items in the string by using PATHLENGTH Iterates the string by using GENERATESERIES… [» Read more](https://www.sqlbi.com/blog/marco/2024/05/18/strings-list-to-table-in-dax/)
- [**Dynamic Pareto analysis in Power BI**](https://www.sqlbi.com/articles/dynamic-pareto-analysis-in-power-bi/)

  This article describes how to implement a dynamic Pareto calculation in Power BI based on a measure that can be selected from a slicer and dynamically filtered by other slicers in the report. [» Read more](https://www.sqlbi.com/articles/dynamic-pareto-analysis-in-power-bi/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, John Thornton

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/generateseries-function](https://docs.microsoft.com/en-us/dax/generateseries-function?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
