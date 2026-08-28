---
title: "DAX : Count number of last known state"
url: "https://dax.tips/2021/05/17/dax-count-number-of-last-known-state"
published: "2021-05-17"
updated: "2021-05-17"
source: "dax.tips"
---

*Using DAX to help group and count the last known state*

I got asked recently to help with a DAX measure for an interesting problem in the inventory domain. The requirement is to generate a chart showing the count of the last known State of a given number of items. Here is a walkthrough of what I was able to come up with, along with an explanation along the way.

The PBIX used for this article can be [downloaded here](https://1drv.ms/u/s!AtDlC2rep7a-gbAdqKcvSgdGrIaUng?e=AetTIV).

The CSV dataset used can be [downloaded here](https://1drv.ms/u/s!AtDlC2rep7a-gbAweMgrIthY5NsVNg?e=9hD0zO).

## The requirement

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B16-1.jpg?resize=657%2C366&ssl=1)

The requirement was simple enough. Take the following dataset and, for any given day, produce a count of each possible State using the last known State for any given **TestID**. The dataset contains six unique Test IDs (A through F). At any given point in time, we first want to establish the last State for each **TestID**. We also want to group this by day and produce a count value for each possible State. Note, a given TestID can have more than one event in a day, and we only care about the last one.

| DateTime | TestID | State |
| --- | --- | --- |
| 1/01/2021 8:00:00 AM | A | Pend |
| 1/01/2021 2:00:00 PM | B | Pend |
| 1/01/2021 8:00:00 PM | C | Pend |
| 2/01/2021 2:00:00 AM | D | Pend |
| 2/01/2021 8:00:00 AM | E | Pend |
| 2/01/2021 2:00:00 PM | F | Pend |
| 2/01/2021 8:00:00 PM | A | Next |
| 3/01/2021 2:00:00 AM | B | Next |
| 3/01/2021 8:00:00 AM | C | Next |
| 3/01/2021 2:00:00 PM | D | Pass |
| 3/01/2021 8:00:00 PM | E | Pass |
| 4/01/2021 2:00:00 AM | F | Pass |
| 4/01/2021 8:00:00 AM | A | Pass |
| 4/01/2021 2:00:00 PM | D | Pass |
| 4/01/2021 8:00:00 PM | D | Fail |
| 5/01/2021 2:00:00 AM | A | Fail |
| 5/01/2021 8:00:00 AM | B | Pass |
| 5/01/2021 2:00:00 PM | C | Pass |

Using the data shown above, the final result should look as follows:

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B8-3.jpg?resize=393%2C353&ssl=1)

Or, in graphical form using a stacked column chart, the result should appear as follows.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B9-1.jpg?resize=460%2C351&ssl=1)

The data model used for this example is as follows:

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B1-2.jpg?resize=1000%2C521&ssl=1)

To clarify the requirement, it helps to look at the data in a Power BI matrix visual with the six states added to Columns, the actual **DateTime** of each event.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B3-2.jpg?resize=998%2C242&ssl=1)

The result for the 1st of Jan should be 3 x Pend.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B4-3.jpg?resize=998%2C443&ssl=1)

The result for the 2nd of Jan should be 1 x Next, 5 x Pend

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B5-3.jpg?resize=998%2C400&ssl=1)

The 3rd of Jan should be 3 x Next, 2 x Pass and 1 x Pend.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B6-2.jpg?resize=998%2C500&ssl=1)

The result for the 4th of Jan should be 3 x Pass, 2 x Next and 1 x Fail.

Note the TestID of D has two items on the same day for the 4th of Jan. We need to ensure the measure uses the correct item. The State for TestID of D at 8 pm should overwrite the earlier event at 2 pm.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B7-3.jpg?resize=999%2C524&ssl=1)

Finally, the result for the 5th of Jan (and every day after that) should be 4 x Pass, and 2 x Fail.

## Step 1: Find the last known value per State

The first step is to work out the DAX expression to determine the last known value for any given TestID by day. Fortunately, DAX has a useful function called [LASTNOTBLANKVALUE](https://docs.microsoft.com/en-us/dax/lastnonblankvalue-function-dax) that helps nicely with this. The function uses a reference to a single column of dates (or passed a single-column table), along with a DAX expression to be evaluated for each date. The result is the first non-blank result for the given DAX expression working back in time chronologically.

The step 1 measure is the following:

```dax
Step 1 Measure = 
VAR currentDate = SELECTEDVALUE('Calendar'[Date]) + 1
RETURN 
    LASTNONBLANKVALUE(
        FILTER(
            ALL('data'[DateTime]),
            'data'[DateTime]<=currentDate
        ),
        CALCULATE(
            MIN('data'[State]),
            REMOVEFILTERS('Calendar'[Date])
        )
    )

```

When the above [Step 1 Measure] gets added to a matrix visual along with the Calendar[Date] and States[State] fields, we get the following output. The row for the 4th of Jan is highlighted in red to show we have the correct results for the day. There should be 3 x Pass, 2 x Next, and 1 x Fail for the day.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B10-2.jpg?resize=654%2C445&ssl=1)

This measure can be broken down as follows.

The **currentDate** variable at line 2 assigns the date value from the row header for any given cell in the matrix. The +1 at the end of the line 2 adds one day to the value. This adjustment means midnight at the end of each day gets used as the cut-off for looking back in time.

The first parameter passed to the [LASTNONBLANKVALUE](https://docs.microsoft.com/en-us/dax/lastnonblankvalue-function-dax) function (lines 5-8) is a table expression containing ALL date-time values from the FACT table that match or are before the value stored in the currentDate variable. The ALL function in line 6 of the FILTER considers all events before the value stored in the currentDate variable.

The second parameter passed to the [LASTNONBLANKVALUE](https://docs.microsoft.com/en-us/dax/lastnonblankvalue-function-dax) function (lines 9-11) determines the State column’s value for each event in the ‘data’ table. Note, the [LASTNONBLANKVALUE](https://docs.microsoft.com/en-us/dax/lastnonblankvalue-function-dax) is iterating over the DateTime column from the ‘data’ table and not one iteration per calendar day. Some TestID values have multiple states per day at different hours, and the DAX expression correctly identifies the single state value for each point in time.

The [REMOVEFILTERS](https://docs.microsoft.com/en-us/dax/removefilters-function-dax) function used in line 11 is vital to eliminate the filter coming from the ‘Calendar'[Date] field used in the axis. If [REMOVEFILTERS](https://docs.microsoft.com/en-us/dax/removefilters-function-dax) function is not used here, the DAX expression will not see rows in the ‘data’ table that occurred before the date shown in the row header.

### Step 2: Count the States per day

Unfortunately, when the above measure gets added to a Matrix Visual grouped by State rather than Test ID, the measure produces the following, which is not the desired result. Ideally, we need a value in each cell that represents a count of the number of times each State occurs in the data.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B11-1.jpg?resize=543%2C469&ssl=1)

To achieve a count, we need to wrap the expression from the original measure with some additional DAX.

```dax
Step 2 Measure = 
VAR currentState = SELECTEDVALUE(States[State])
VAR currentDate = SELECTEDVALUE('Calendar'[Date])+1
RETURN
    CONCATENATEX(
        ALL(Tests[TestID]),
        LASTNONBLANKVALUE(
            FILTER(
                ALL('data'[DateTime]),
                [DateTime]<=currentDate
            ),
            CALCULATE(
                MAX('data'[State])
                ,REMOVEFILTERS('Calendar'[Date])
                )
            ),
        ","
        )

```

Taking a baby-steps approach to achieving the correct count, I know I’m going to need a DAX Iterator function. To ensure I get valid values, I often start with the [CONCATENATEX](https://docs.microsoft.com/en-us/dax/concatenatex-function-dax) function to show the values in a Power BI Visual.

In this measure, lines 7-15 are the same as our measure from step 1. The main change is this logic is now wrapped with a [CONCATENATEX](https://docs.microsoft.com/en-us/dax/concatenatex-function-dax) function that performs an iterator role. For every cell in the matrix visual, the [CONCATENATEX](https://docs.microsoft.com/en-us/dax/concatenatex-function-dax) function will perform exactly six loops. I like to think of DAX iterator functions as FOREACH functions.

In this case, the [CONCATENATEX](https://docs.microsoft.com/en-us/dax/concatenatex-function-dax) function performs a loop for every distinct value found in the Tests[TestID] column and builds a comma-separated string showing the output for each loop. The number of commas helps confirm we performed the expected number of loops. Five commas mean we iterated six times.

When the [Step 2 Measure] gets used in a matrix visual along with the Calendar[Date] column, we see the following result:

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B12-1.jpg?resize=729%2C713&ssl=1)

This result is looking better. For example, this measure for January 4 shows we have 3 x Pass, 2 x Next, and 1 x Fail. The last step is to introduce the State[States] column into the column header and filter/count the results.

### Step 3: Filter and Count the results

The final version of the measure replaces the [CONCATENATEX](https://docs.microsoft.com/en-us/dax/concatenatex-function-dax) iterator with a [FILTER](https://docs.microsoft.com/en-us/dax/filter-function-dax) function, also an iterator. The FILTER function allows us to add some filtering logic to return a row where the **State** output for each loop of the TestID happens to match the **State** for the column-header for each cell in the matrix visual.

The filter logic uses the currentState variable, which gets assigned the value for the column header at line 2. The variable gets used in line 18 inside each loop.

We intend to add the States[State] column to the axis (or header) for the final visual. So, a REMOVEFILTERS function is added at line 16 to prevent the current value from the axis (or row/column header) filtering the calculation inside the LASTNONBLANKVALUE where we need to see ALL historic states for a given Test.

Finally, the FILTER function gets wrapped inside a COUNTROWS function that counts the number of rows in the table expression returned by the FILTER function.

The updated measure is now as follows:

```dax
LNBV Measure = 
VAR currentState = SELECTEDVALUE(States[State])
VAR currentDate = SELECTEDVALUE('Calendar'[Date])+1
RETURN
    COUNTROWS(
        FILTER(
            ALL(Tests[TestID]),
            LASTNONBLANKVALUE(
                FILTER(
                    ALL('data'[DateTime]),
                    [DateTime]<=currentDate
                ),
                CALCULATE(
                    MAX('data'[State])
                    ,REMOVEFILTERS('Calendar'[Date])
                    ,REMOVEFILTERS('States'[State])
                    )
                ) = currentState
            ) 
        )

```

When this updated measure now gets used in a matrix visual with ‘Calendar'[Date] and ‘States'[State], we see the following:

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B13-1.jpg?w=1000&ssl=1)

The same measure used in a stacked column chart:

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B9-1.jpg?resize=614%2C468&ssl=1)

## Alternative Version

As with all DAX calculations, there is often more than one way to write the expression and still return the same result. Sometimes different versions can perform better (or worse) depending on the unique number of values stored in each column. A good idea when working with any calculation is to study the output of the measure using the server timings feature of DAX Studio.

The results from our current measure are as follows:

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B14.jpg?resize=840%2C234&ssl=1)

We are only testing with a small number of rows, but this mostly looks good. We are completing the query in 27 ms and only using 5 Storage Engine (VertiPaq) queries. However, the final SE query on the right-hand side is in bold – which means we are performing a callback. This callback doesn’t always mean a bad thing; however, it could indicate a potential problem once the measure runs over a lot of data and under high concurrency. Callbacks often mean the engine cannot take advantage of cache to help speed up calculations.

Here is an interesting alternative version of the measure that produces the same query results.

```dax
Using TopN = 
VAR currentState = SELECTEDVALUE(States[State])
VAR currentDate = SELECTEDVALUE('Calendar'[Date])+1
RETURN
    COUNTROWS(
        FILTER(
            ALL( data[TestID] ),
            SELECTCOLUMNS(
                TOPN(
                    1,
                    CALCULATETABLE( 
                        FILTER( 'data', 
                        'data'[DateTime] < currentDate
                        )
                        ,REMOVEFILTERS('Calendar'[Date])
                        ,REMOVEFILTERS('States'[State])
                        ),
                    'data'[DateTime], 
                    DESC
                ),
                "Last Value", [State]
                )
            = currentState
            )
        )

```

The structure of the alternative version is essentially the same as the original measure. However, in this version, the LASTNONBLANKVALUE function gets replaced by a [TOPN](https://docs.microsoft.com/en-us/dax/topn-function-dax) at line 9.

The idea behind using TOPN is to generate a list of DateTime values in descending order. Then for each DateTime value, use the value from the [State] column. Note, the FILTER function uses the entire ‘data’ table to have access to all columns in the table (including the [State]) column. When the top 1 row gets selected in descending order, the SELECTCOLUMNS function at line 8 correctly returns the most recent State for any given TestID.

The advantage of this version is we get one less SE scan and no longer see a callback in the server timings output.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B15.jpg?resize=1000%2C288&ssl=1)

## Video Walk-through

## Summary

In summary, I think these are both pretty good solutions to what can seem like a tricky problem to solve. Other DAX expressions will produce the same result. I wanted to use this article to walk through and describe the baby-step approach I used recently.

I welcome your comments and feedback as always.

5
9
votes

Article Rating
