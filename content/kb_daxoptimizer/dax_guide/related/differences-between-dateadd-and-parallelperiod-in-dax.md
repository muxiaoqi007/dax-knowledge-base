---
title: "Differences between DATEADD and PARALLELPERIOD in DAX"
url: "https://www.sqlbi.com/articles/differences-between-dateadd-and-parallelperiod-in-dax"
published: "2024-01-30"
updated: 
source: "sqlbi.com"
---

# Differences between DATEADD and PARALLELPERIOD in DAX

> 发布：2024-01-30

In DAX we have several time intelligence functions that are just syntax sugar for invoking base functions with more parameters. Using these more straightforward functions is very convenient, but understanding what happens under the hood is a good idea to better control the behavior of the measures you write.

## Introducing [[DATEADD]] and [[PARALLELPERIOD]]

Many DAX time intelligence functions that “shift” the range of dates passed as a parameter are based on two functions: [[DATEADD]] and [[PARALLELPERIOD]]. At first sight, they seem similar in the sense that if you have a year selected, both these functions return the dates in the previous year:

Measure in Sales table

```dax


Sales Amount -1Y DATEADD = 

CALCULATE ( 

    [Sales Amount],

    DATEADD ( 'Date'[Date], -1, YEAR )

)

```

Measure in Sales table

```dax


Sales Amount -1Y PARALLELPERIOD = 

CALCULATE ( 

    [Sales Amount],

    PARALLELPERIOD ( 'Date'[Date], -1, YEAR )

)

```

These two measures return the same value for each year.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-72.png)

However, we can see the differences as soon as we drill down to the month level.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-69.png)

The month level shows different numbers: for [[DATEADD]] we have the value of the same month in the previous year, whereas for [[PARALLELPERIOD]] we always get the total for the whole previous year, regardless of the month selected.

One last explanation about the total: The value is the same for [[DATEADD]] and [[PARALLELPERIOD]] because it returns all the years, but the last one is in the *Date* table. The row with the total has an empty filter context; the entire content of the *Date* table (from 2017 to 2020) is passed to the time intelligence functions, which return the 2017-2019 range of years (because 2016 is not present in the *Date* table).

![](https://cdn.sqlbi.com/wp-content/uploads/image3-61.png)

Thus, it seems that [[DATEADD]] and [[PARALLELPERIOD]] display the same behavior when we consider the same level of the interval argument ([[YEAR]] in the examples) and a different behavior when we consider a more detailed level than the one passed to the interval argument, such as the month. In reality, there are a few more details that are worth knowing.

## Understanding [[DATEADD]]

The algorithm implemented in [[DATEADD]] analyzes the calendar months specified in the first argument.

1. If the interval is [[DAY]], every *Date[Date]* value is shifted by the number of intervals specified. The last day of the shifted month is returned if the day does not exist in the shifted month.
2. If the interval is [[MONTH]], [[QUARTER]], or [[YEAR]], then for each month:
   1. If all the days of the month are selected, then the entire month is shifted by the number of intervals specified.
   2. If not all the days of the month are selected, then each day is shifted by the number of intervals specified.

Remember that when it comes to the DAX time intelligence functions, saying that an entire month is selected means that all the values in the *Date* table for that month are visible in the filter context. This algorithm is required to return the 31 days in January in case February (which has only 28 or 29 days) is selected. Indeed, the only column used is *Date[Date]*; the other columns in the *Date* table are unknown to the time intelligence functions.

For example, the following query shifts the days in February back by one day:

```dax


EVALUATE

VAR February2019 =

    DATESBETWEEN ( 'Date'[Date], dt"2019-02-01", dt"2019-02-28" )

VAR ApplyDateAdd =

    DATEADD ( February2019, -1, DAY )

VAR Result =

    ROW (

        "MIN", MINX ( ApplyDateAdd, 'Date'[Date] ),

        "MAX", MAXX ( ApplyDateAdd, 'Date'[Date] ),

        "Days", COUNTROWS ( ApplyDateAdd )

    )

RETURN

    Result

```

| MIN | MAX | Days |
| --- | --- | --- |
| 2019-01-31 | 2019-02-27 | 28 |

The result is still 28 days, from 1/31/2019 to 2/27/2019. If we shift for one month, the result includes a different number of days:

```dax


EVALUATE

VAR February2019 =

    DATESBETWEEN ( 'Date'[Date], dt"2019-02-01", dt"2019-02-28" )

VAR ApplyDateAdd =

    DATEADD ( February2019, -1, MONTH )

VAR Result =

    ROW (

        "MIN", MINX ( ApplyDateAdd, 'Date'[Date] ),

        "MAX", MAXX ( ApplyDateAdd, 'Date'[Date] ),

        "Days", COUNTROWS ( ApplyDateAdd )

    )

RETURN

    Result

```

| MIN | MAX | Days |
| --- | --- | --- |
| 2019-01-01 | 2019-01-31 | 31 |

The result is now 31 days, the number of days in January: [[DATEADD]] received 28 days and returned 31. If we include a full month and a few days of another month, then [[DATEADD]] applies two different rules depending on the month. For example, if we provide the range from February 1st to March 10th, the input has 38 days:

```dax


EVALUATE

VAR FebruaryMarch2019 =

    DATESBETWEEN ( 'Date'[Date], dt"2019-02-01", dt"2019-03-10" )

VAR ApplyDateAdd =

    DATEADD ( FebruaryMarch2019, -1, MONTH )

VAR Result =

    ROW (

        "MIN", MINX ( ApplyDateAdd, 'Date'[Date] ),

        "MAX", MAXX ( ApplyDateAdd, 'Date'[Date] ),

        "Days", COUNTROWS ( ApplyDateAdd )

    )

RETURN

    Result

```

| MIN | MAX | Days |
| --- | --- | --- |
| 2019-01-01 | 2019-02-10 | 41 |

The result is 41 days: 31 days in January 2019, plus the first 10 days in February 2019.

[[DATEADD]] also applies the month-based algorithm when the interval is [[QUARTER]] or [[YEAR]]. This way, [[DATEADD]] respects the different number of days in months and quarters, such as an additional day in leap years.

## Understanding [[PARALLELPERIOD]]

The algorithm implemented in [[PARALLELPERIOD]] is based on the interval specified in the third argument. For each interval with at least one value in *Date[Date]*, the function returns the entire interval shifted by the number of specified intervals. [[PARALLELPERIOD]] can use [[YEAR]], [[QUARTER]], and [[MONTH]], and does not support the [[DAY]] interval.

For example, we can provide the range of dates between February 1st and March 10th, or just one day in February and one in March: the result is the same for [[YEAR]], [[QUARTER]], and [[MONTH]]. By specifying 0 in the second argument, the result returns the current interval for the dates specified in the first argument:

```dax


EVALUATE

VAR Selection =

    DATESBETWEEN ( 'Date'[Date], dt"2019-02-01", dt"2019-03-10" )

    // You can replace DATESBETWEEN with just two days, 

    // one in each February and one in March:

    // the result is the same

    //

    // TREATAS ( { dt"2019-02-01", dt"2019-03-10" }, 'Date'[Date] )

VAR CurrentYear =

    PARALLELPERIOD ( Selection, 0, YEAR )

VAR CurrentQuarter =

    PARALLELPERIOD ( Selection, 0, QUARTER )

VAR CurrentMonth =

    PARALLELPERIOD ( Selection, 0, MONTH )

VAR Result =

    SELECTCOLUMNS (

        {

            ( "CurrentYear", 

              MINX ( CurrentYear, 'Date'[Date] ), 

              MAXX ( CurrentYear, 'Date'[Date] ), 

              COUNTROWS ( CurrentYear ) ),

            ( "CurrentQuarter", 

              MINX ( CurrentQuarter, 'Date'[Date] ), 

              MAXX ( CurrentQuarter, 'Date'[Date] ), 

              COUNTROWS ( CurrentQuarter ) ),

            ( "CurrentMonth", 

              MINX ( CurrentMonth, 'Date'[Date] ), 

              MAXX ( CurrentMonth, 'Date'[Date] ), 

              COUNTROWS ( CurrentMonth ) )

        },

        "Transformation", [Value1],

        "MIN", [Value2],

        "MAX", [Value3],

        "Days", [Value4]

    )

RETURN

    Result

```

| Transformation | MIN | MAX | Days |
| --- | --- | --- | --- |
| CurrentYear | 2019-01-01 | 2019-12-31 | 365 |
| CurrentQuarter | 2019-01-01 | 2019-03-31 | 90 |
| CurrentMonth | 2019-02-01 | 2019-03-31 | 59 |

By using a negative number, [[PARALLELPERIOD]] retrieves periods back in time. For example, by using -1, [[PARALLELPERIOD]] returns the previous periods for each of the intervals detected in the input:

```dax


EVALUATE

VAR Selection =

    DATESBETWEEN ( 'Date'[Date], dt"2019-02-01", dt"2019-03-10" )

    // You can replace DATESBETWEEN with just two days, 

    // one in February and one in March:

    // the result is the same

    //

    // TREATAS ( { dt"2019-02-01", dt"2019-03-10" }, 'Date'[Date] )

VAR _PreviousYear =

    PARALLELPERIOD ( Selection, -1, YEAR )

VAR _PreviousQuarter =

    PARALLELPERIOD ( Selection, -1, QUARTER )

VAR _PreviousMonth =

    PARALLELPERIOD ( Selection, -1, MONTH )

VAR Result =

    SELECTCOLUMNS (

        {

            ( "PreviousYear", 

              MINX ( _PreviousYear, 'Date'[Date] ), 

              MAXX ( _PreviousYear, 'Date'[Date] ), 

              COUNTROWS ( _PreviousYear ) ),

            ( "PreviousQuarter", 

              MINX ( _PreviousQuarter, 'Date'[Date] ), 

              MAXX ( _PreviousQuarter, 'Date'[Date] ), 

              COUNTROWS ( _PreviousQuarter ) ),

            ( "PreviousMonth", 

              MINX ( _PreviousMonth, 'Date'[Date] ), 

              MAXX ( _PreviousMonth, 'Date'[Date] ), 

              COUNTROWS ( _PreviousMonth ) )

        },

        "Transformation", [Value1],

        "MIN", [Value2],

        "MAX", [Value3],

        "Days", [Value4]

    )

RETURN

    Result

```

| Transformation | MIN | MAX | Days |
| --- | --- | --- | --- |
| PreviousYear | 2018-01-01 | 2018-12-31 | 365 |
| PreviousQuarter | 2018-10-01 | 2018-12-31 | 92 |
| PreviousMonth | 2019-01-01 | 2019-02-28 | 59 |

We used as input the same range or set of dates in February and March for the example that returns the current period. In this case, we get:

- All of 2018 for [[YEAR]];
- Q4 of 2018 for [[QUARTER]] (our input dates are in Q1 2019);
- January and February of 2019 for [[MONTH]] (our input dates are within February and March 2019).

## Functions that use [[DATEADD]] or [[PARALLELPERIOD]]

The [[SAMEPERIODLASTYEAR]] function is the only one that internally uses [[DATEADD]]. Indeed, when you write:

```dax


SAMEPERIODLASTYEAR ( 'Date'[Date] )

```

In reality, the code executed is the following:

```dax


DATEADD ( 'Date'[Date], -1, YEAR )

```

We call this “syntax sugar”: [[SAMEPERIODLASTYEAR]] is just a different way to invoke [[DATEADD]] without specifying the second and third arguments, which are always -1 and [[YEAR]]. These functions simplify writing the code, making it more readable – however, there are no differences between using one syntax or the other.

There are another eight functions that are “syntax sugar” for [[DATEADD]] or [[PARALLELPERIOD]], but they always return a single interval even if the input has multiple periods:

- [[PREVIOUSDAY]]: Returns the day before the first day of the selected dates.
- [[PREVIOUSMONTH]]: Returns the whole month before the first day of the selected dates.
- [[PREVIOUSQUARTER]]: Returns the whole quarter before the first day of the selected dates.
- [[PREVIOUSYEAR]]: Returns the whole year before the first day of the selected dates.
- [[NEXTDAY]]: Returns the day after the last day of the selected dates.
- [[NEXTMONTH]]: Returns the whole month after the last day of the selected dates.
- [[NEXTQUARTER]]: Returns the whole quarter after the last day of the selected dates.
- [[NEXTYEAR]]: Returns the whole year after the last day of the selected dates.

In the last examples of [[PARALLELPERIOD]], we used variable names such as *\_PreviousMonth*, *\_PreviousQuarter*, and *\_PreviousYear*. However, the corresponding DAX functions have a slightly different behavior: they only use the first of the selected dates, so the result has no more than one interval (day, month, quarter, or year). [[PREVIOUSMONTH]], [[PREVIOUSQUARTER]], and [[PREVIOUSYEAR]] internally use [[PARALLELPERIOD]] with [[FIRSTDATE]], whereas [[PREVIOUSDAY]] uses [[DATEADD]] with [[FIRSTDATE]]. The functions with a [[NEXT]] prefix use the same technique, providing a positive number of intervals and [[LASTDATE]] instead of [[FIRSTDATE]] as input.

The following code shows, for each [[PREVIOUS]]\*/NEXT\* function, the corresponding syntax using DATEADD/PARALLELPERIOD in the following line:

```dax


PREVIOUSYEAR ( Selection )      

PARALLELPERIOD ( FIRSTDATE ( Selection ), -1, YEAR )



PREVIOUSQUARTER ( Selection )

PARALLELPERIOD ( FIRSTDATE ( Selection ), -1, QUARTER )



PREVIOUSMONTH ( Selection )

PARALLELPERIOD ( FIRSTDATE ( Selection ), -1, MONTH )



PREVIOUSDAY ( Selection )

DATEADD ( FIRSTDATE ( Selection ), -1, DAY )



NEXTYEAR ( Selection )

PARALLELPERIOD ( LASTDATE ( Selection ), 1, YEAR )



NEXTQUARTER ( Selection )

PARALLELPERIOD ( LASTDATE ( Selection ), 1, QUARTER )



NEXTMONTH ( Selection ) 

PARALLELPERIOD ( LASTDATE ( Selection ), 1, MONTH )



NEXTDAY ( Selection ) 

DATEADD ( LASTDATE ( Selection ), 1, DAY )

```

The following example shows that [[PREVIOUS]]\* functions are based on the first date in the input dates, whereas the [[NEXT]]\* functions are based on the last date in the input dates:

```dax


EVALUATE

VAR Selection =

    DATESBETWEEN ( 'Date'[Date], dt"2018-02-12", dt"2019-03-10" )

    // You can replace DATESBETWEEN with just two days, 

    // one in February and one in March:

    // the result is the same

    //

    // TREATAS ( { dt"2018-02-12", dt"2019-03-10" }, 'Date'[Date] )

VAR _PreviousYear =

    PREVIOUSYEAR ( Selection )

    // PARALLELPERIOD ( FIRSTDATE ( Selection ), -1, YEAR )

VAR _PreviousQuarter =

    PREVIOUSQUARTER ( Selection )

    // PARALLELPERIOD ( FIRSTDATE ( Selection ), -1, QUARTER )

VAR _PreviousMonth =

    PREVIOUSMONTH ( Selection ) 

    // PARALLELPERIOD ( FIRSTDATE ( Selection ), -1, MONTH )

VAR _PreviousDay =

    PREVIOUSDAY ( Selection ) 

    // DATEADD ( FIRSTDATE ( Selection ), -1, DAY )

VAR _NextYear =

    NEXTYEAR ( Selection )

    // PARALLELPERIOD ( LASTDATE ( Selection ), 1, YEAR )

VAR _NextQuarter =

    NEXTQUARTER ( Selection )

    // PARALLELPERIOD ( LASTDATE ( Selection ), 1, QUARTER )

VAR _NextMonth =

    NEXTMONTH ( Selection ) 

    // PARALLELPERIOD ( LASTDATE ( Selection ), 1, MONTH )

VAR _NextDay =

    NEXTDAY ( Selection ) 

    // DATEADD ( LASTDATE ( Selection ), 1, DAY )

VAR Result =

    SELECTCOLUMNS (

        {

            ( "PreviousYear", 

              MINX ( _PreviousYear, 'Date'[Date] ), 

              MAXX ( _PreviousYear, 'Date'[Date] ), 

              COUNTROWS ( _PreviousYear ) ),

            ( "PreviousQuarter", 

              MINX ( _PreviousQuarter, 'Date'[Date] ), 

              MAXX ( _PreviousQuarter, 'Date'[Date] ), 

              COUNTROWS ( _PreviousQuarter ) ),

            ( "PreviousMonth", 

              MINX ( _PreviousMonth, 'Date'[Date] ), 

              MAXX ( _PreviousMonth, 'Date'[Date] ), 

              COUNTROWS ( _PreviousMonth ) ),

            ( "PreviousDay", 

              MINX ( _PreviousDay, 'Date'[Date] ), 

              MAXX ( _PreviousDay, 'Date'[Date] ), 

              COUNTROWS ( _PreviousDay ) ),

            ( "NextYear", 

              MINX ( _NextYear, 'Date'[Date] ), 

              MAXX ( _NextYear, 'Date'[Date] ), 

              COUNTROWS ( _NextYear ) ),

            ( "NextQuarter", 

              MINX ( _NextQuarter, 'Date'[Date] ), 

              MAXX ( _NextQuarter, 'Date'[Date] ), 

              COUNTROWS ( _NextQuarter ) ),

            ( "NextMonth", 

              MINX ( _NextMonth, 'Date'[Date] ), 

              MAXX ( _NextMonth, 'Date'[Date] ), 

              COUNTROWS ( _NextMonth ) ),

            ( "NextDay", 

              MINX ( _NextDay, 'Date'[Date] ), 

              MAXX ( _NextDay, 'Date'[Date] ), 

              COUNTROWS ( _NextDay ) )

        },

        "Transformation", [Value1],

        "MIN", [Value2],

        "MAX", [Value3],

        "Days", [Value4]

    )

RETURN

    Result

```

| Transformation | MIN | MAX | Days |
| --- | --- | --- | --- |
| PreviousYear | 2017-01-01 | 2017-12-31 | 365 |
| PreviousQuarter | 2017-10-01 | 2017-12-31 | 92 |
| PreviousMonth | 2018-01-01 | 2018-01-31 | 31 |
| PreviousDay | 2018-02-11 | 2018-02-11 | 1 |
| NextYear | 2020-01-01 | 2020-12-31 | 366 |
| NextQuarter | 2019-04-01 | 2019-06-30 | 91 |
| NextMonth | 2019-04-01 | 2019-04-30 | 30 |
| NextDay | 2019-03-11 | 2019-03-11 | 1 |

The main difference between using [[PREVIOUS]]\*/NEXT\* functions instead of [[PARALLELPERIOD]] is the behavior when multiple intervals are selected. This difference is especially visible in the total: [[PREVIOUSYEAR]] displays the same behavior as [[PARALLELPERIOD]] when a single year or month is selected, but [[PREVIOUSYEAR]] returns blank whenever multiple years are in the filter context, like in the total.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-55.png)

*Sales Amount [[PREVIOUSYEAR]]* returns blank in the total row because it tries to retrieve 2016, the year before the first year within the *Date* table (2017). The result is blank because there are no dates in *Date* before 2017.

In contrast, the *Sales Amount -1 [[PARALLELPERIOD]]* measure returns the total sum of all the years in the *Date* table except the last one (2020).

![](https://cdn.sqlbi.com/wp-content/uploads/image5-47.png)

Thus, [[PARALLELPERIOD]] is used by [[PREVIOUS]]\*/NEXT\* time intelligence functions together with the FIRSTDATE/LASTDATE functions to reduce the selection to a single interval – which explains the different behavior we observed in the last examples.

## Conclusions

The time intelligence functions in DAX are often a shortcut to invoking a longer syntax. [[DATEADD]] and [[PARALLELPERIOD]] are no exceptions: they are similar when used to compare one of the intervals specified in the argument but differ when used at different granularities when multiple intervals are involved. This behavior affects the functions derived from them: [[SAMEPERIODLASTYEAR]], FIRSTDAY, FIRSTMONTH, FIRSTQUARTER, FIRSTYEAR, LASTDAY, LASTMONTH, LASTQUARTER, and LASTYEAR internally use [[DATEADD]], [[PARALLELPERIOD]], [[FIRSTDATE]], and [[LASTDATE]]. Make sure you control the behavior of your measures by paying attention to the expanded syntax before choosing the time intelligence function to use.

[[DATEADD]]

Context transition

Moves the given set of dates by a specified interval.

`DATEADD ( <Dates>, <NumberOfIntervals>, <Interval> [, <Extension>] [, <Truncation>] )`

[[PARALLELPERIOD]]

Context transition

Returns a parallel period of dates by the given set of dates and a specified interval.

`PARALLELPERIOD ( <Dates>, <NumberOfIntervals>, <Interval> )`

[[YEAR]]

Returns the year of a date as a four digit integer.

`YEAR ( <Date> )`

[[DAY]]

Returns a number from 1 to 31 representing the day of the month.

`DAY ( <Date> )`

[[MONTH]]

Returns a number from 1 (January) to 12 (December) representing the month.

`MONTH ( <Date> )`

[[QUARTER]]

Returns a number from 1 (January-March) to 4 (October-December) representing the quarter.

`QUARTER ( <Date> )`

[[SAMEPERIODLASTYEAR]]

Context transition

Returns a set of dates in the current selection from the previous year.

`SAMEPERIODLASTYEAR ( <Dates> )`

[[PREVIOUSDAY]]

Context transition

Returns a previous day.

`PREVIOUSDAY ( <Dates> )`

[[PREVIOUSMONTH]]

Context transition

Returns a previous month.

`PREVIOUSMONTH ( <Dates> )`

[[PREVIOUSQUARTER]]

Context transition

Returns a previous quarter.

`PREVIOUSQUARTER ( <Dates> )`

[[PREVIOUSYEAR]]

Context transition

Returns a previous year.

`PREVIOUSYEAR ( <Dates> [, <YearEndDate>] )`

[[NEXTDAY]]

Context transition

Returns a next day.

`NEXTDAY ( <Dates> )`

[[NEXTMONTH]]

Context transition

Returns a next month.

`NEXTMONTH ( <Dates> )`

[[NEXTQUARTER]]

Context transition

Returns a next quarter.

`NEXTQUARTER ( <Dates> )`

[[NEXTYEAR]]

Context transition

Returns a next year.

`NEXTYEAR ( <Dates> [, <YearEndDate>] )`

[[FIRSTDATE]]

Context transition

Returns first non blank date.

`FIRSTDATE ( <Dates> )`

[[NEXT]]

The Next function retrieves a value in the next row of an axis in the Visual Calculation data grid.

`NEXT ( <Column> [, <Steps>] [, <Axis>] [, <OrderBy>] [, <Blanks>] [, <Reset>] )`

[[LASTDATE]]

Context transition

Returns last non blank date.

`LASTDATE ( <Dates> )`

[[PREVIOUS]]

The Previous function retrieves a value in the previous row of an axis in the Visual Calculation data grid.

`PREVIOUS ( <Column> [, <Steps>] [, <Axis>] [, <OrderBy>] [, <Blanks>] [, <Reset>] )`
