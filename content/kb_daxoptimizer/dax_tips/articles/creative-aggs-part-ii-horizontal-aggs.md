---
title: "Creative Aggs Part II : Horizontal Aggs"
url: "https://dax.tips/2019/10/21/creative-aggs-part-ii-horizontal-aggs"
published: "2019-10-21"
updated: "2019-11-14"
source: "dax.tips"
---

- [[creative-aggs-part-i-introduction|Introduction to Aggs]]
- [[creative-aggs-part-ii-horizontal-aggs|Horizontal Aggs]] (this article)
  - Mixing Agg and base table to produce a single value
- [[creative-aggs-part-iii-accordion-aggs|Accordion Aggs]]
  - Variable grain aggs in a single table for extra flexibility
- [[creative-aggs-part-iv-filtered-aggs|Filtered Aggs]]
  - Using Composite Models to keep recent data in memory, and older data in the source to optimise the model size
- [[creative-aggs-part-v-incremental-aggs|Incremental Aggs]]
  - A smart way to update Agg tables to optimise refresh time
- [[creative-aggs-part-vi-shadow-models|Shadow Models]]
  - Create manual DUAL mode tables to work around some current limitations of Agg Awareness

Part II of this series on creative aggs looks at a specific technique I like to call Horizontal Aggs. The theory behind this particular technique is relatively straight forward, although the mechanics involved in applying will appear complex. I found this approach quite interesting and it did yield some decent performance gains under load on some pretty big models.

### The Theory

A common aggregation strategy in AS models is to use the Time dimension as the first dimension to build Aggregates against. The main advantage the Time dimension offers is better stability of member groupings and how they relate to each other. A specific day will always belong the same month, and will not bounce to another month. This is the same as week, month and year.

Whereas with dimensions like Product, an item can change category. A site in a Store dimension may move location or change banner. Staff can move teams etc. A Time dimension not only provides better stability but is presumably the most heavily used dimension in your model, so can often provide the easiest gains when optimising with Aggs.

Consider the following a model with the following characteristics :

- The model is hosted in Azure Analysis Services – no composite model or agg awareness available
- A FACT table with billions of rows of retail data at Day/Store/Product
- An Aggregation table of the above table at **Week**/Store/Product
- A second Aggregation table of the above FACT table at **Month**/Store/Product
- Analysis of user queries show most queries are for date ranges rather than individual days e.g. specific weeks, months or periods like “Last 100 days”

Horizontal Aggs works by breaking down a user query by date and trying to see if parts (or all) the query can be more efficiently delivered using a mixture of the base table with either the week or month aggregation tables.

The technique uses Explicit DAX to understand the date ranges (including any gaps) involved and building a fast table to establish where there are blocks of complete weeks or months that can be satisfied from an agg table – and if this is the case, to supplement any leftover dates from the base table.

Consider a query that wants to SUM a column for a 123-day range between the 24th November 2018 to the 27th March 2019.

A good deal of this date range could be quickly retrieved from a Monthly aggregate table. In fact, all values between the 1st of December 2018 and the 28th of February 2019 could be covered using a Monthly Agg table based on a calendar month. The Monthly Agg table would have far fewer rows to scan to generate this number.

Then, the value calculated using the Monthly table could be added to a second SUM that runs over the days before and after the monthly blocks.

![](https://i1.wp.com/dax.tips/wp-content/uploads/2019/10/B1-3.png?fit=1000%2C375&ssl=1)

Horizontal Aggs using Monthly Agg table

In the image above, a calculation could use a Monthly agg table to produce a single value for December 2018, January 2019 and February 2019. This value could then be added to a calculation over the base table for the last 7-days of November 2018 and the first 27 days of March.

The calculation would only scan the larger base table for 34 days compared with 123 days and should complete in approx. a quarter of the time. The additional time required to get the monthly data from the Agg table should still result in a faster overall query.

The good news is we can do better by testing if a weekly based agg might be a better fit.

Using a weekly agg based on a Sunday start to the week, of the 123 days in the period, only 4 days need to come from the base table. The remaining days can all be derived from 18 blocks from the weekly agg table.

![](https://i2.wp.com/dax.tips/wp-content/uploads/2019/10/B2-4.png?fit=1000%2C341&ssl=1)

Horizontal Aggs from Weekly table

The table below provides a simplified example of what you might expect if your base table has approximately 50 million rows per day. The table also assumes data is evenly distributed to the point where a monthly agg table is 30 times smaller than the base table and the weekly agg table is 7 times smaller. The reality is rarely this perfect, but should still mean fewer rows would need to be scanned when using horizontal aggs.

|  | Base Table | Monthly Agg | Weekly Agg | Total Row Scanned | % of Total |
| --- | --- | --- | --- | --- | --- |
| No Aggs | 123 x 50M |  |  | 6,150,000,000 |  |
| Monthly Agg | 34 x 50M | 3 x 1.6M |  | 1,705,000,000 | 28% |
| Weekly | 4 x 50M |  | 18 x 7.1M | 328,571,429 | 5% |

The values shown here suggest that if a weekly based aggregate table was used for the 123 days between 24th Nov 2018 and 27th of March 2019, only 328 Million rows would need to be scanned all up. This represents around only 5% of the rows needing to be scanned without aggs.

The more data you have, the better this approach works. However, if your base table is smaller than a billion rows, the overhead involved to scan both the base and agg tables may result in slower overall performance.

I provide an example showing how this can be applied in DAX that only scans the base table if required. If most reporting periods used in your reports align nicely with the aggregation periods used by a week or month agg table, then you should still see some benefit in smaller models.

### The Practice

The following example looks long and complicated at first glance, but I will walk through each step explaining what each step is doing as part of the overall calculation.

#### Step 1

Establish a set of dates in a table variable. This example uses a range of dates between 27 July 2019 and 2 Sept 2019.

```dax
Horizontal Agg Measure = 
VAR mySelectedDates =
    FILTER (
        VALUES ( 'dim Date'[DateKey] ),
        [DateKey] >= DATE ( 2019, 7, 27 )
            && [dateKey] <= DATE ( 2019, 9, 2 )
    )

```

The contents of the **mySelectedDates** table variable is as follows:

![](https://i1.wp.com/dax.tips/wp-content/uploads/2019/10/B3-3.png?fit=180%2C1024&ssl=1)

#### Step 2

Build a list of days that are **not** selected and do not exist in the table variable assigned to mySelectedDates. No date value should exist in both table variables and there should be no gaps.

```dax
VAR myNonSelectedDates =
    SELECTCOLUMNS (
        EXCEPT ( ALL ( 'dim Date'[DateKey] ), mySelectedDates ),
        "GapDay", [DateKey]
    )

```

This single-column table will be larger than the table generated in Step 1.

#### Step 3

This section is where the core magic of Horiztonal Aggs happens. Two additional columns get added to the table variable created in Step 1. The first additional column is called [Montly Agg Cover] and **only** carries a value if the date on that row is part of a complete monthly batch. If the month has any gaps it leaves a blank.

The same happens for the second additional column called [Weekly Agg Cover]. If the date in the first column of the row is part of a complete weekly batch, a date representing the first day of the week is stored in the new column.

```dax
VAR CheckMonthAndWeek =
    ADDCOLUMNS (
        mySelectedDates,
        "Monthly Agg Cover", IF (
            COUNTROWS (
                FILTER (
                    myNonSelectedDates,
                    MONTH ( [DateKey] ) = MONTH ( [GapDay] )
                        && YEAR ( [DateKey] ) = YEAR ( [GapDay] )
                )
            ) = 0,
            STARTOFMONTH ( 'dim Date'[DateKey] )
        ),
        "Weekly Agg Cover",
        VAR myStartOfWeek =
            (
                [DateKey]
                    - ( WEEKDAY ( [DateKey], 1 ) - 1 )
            )
        RETURN
            IF (
                COUNTROWS (
                    FILTER (
                        myNonSelectedDates,
                        myStartOfWeek
                            = (
                                [GapDay]
                                    - ( WEEKDAY ( [GapDay], 1 ) - 1 )
                            )
                    )
                ) = 0,
                [DateKey]
                    - ( WEEKDAY ( [DateKey], 1 ) - 1 )
            )
    )

```

You will see gaps in the top and bottom of the two new columns. These gaps exist because the dates for these rows cannot be safely retrieved from either a monthly or weekly based agg tables.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B4-3.png?fit=603%2C1024&ssl=1)

#### Step 4

The next step is to add 2 more columns that will carry the signal showing which dates need to be retrieved from the base table depending on which aggregate table is used.

```dax
VAR SummaryOfData =
    ADDCOLUMNS (
        CheckMonthAndWeek,
        "Monthly Detail from Day Table", IF ( ISBLANK ( [Monthly Agg Cover] ), [DateKey] ),
        "Weekly Detail from Day Table", IF ( ISBLANK ( [Weekly Agg Cover] ), [DateKey] )
    )

```

You will see there are 5 dates populated at the top of the [Monthly detail from day table] and 2 at the bottom. Appropriate values are also populated in the [Weekly detail from day table] column. These dates represent what will be retrieved from the base table. We can now see a picture showing which dates can be obtained from agg tables and those still need to be retrieved from a base table.

![](https://i2.wp.com/dax.tips/wp-content/uploads/2019/10/B5-3.png?fit=1000%2C970&ssl=1)

#### Step 5

The next step creates 4 variables that summarise the table generated in step 4. These are the unique dates that will be used as filters over the relevant base or agg table in the final calculation. In this example, the table assigned to the MonthlyAggDates variable only contains a single value (2019-08-01), while the table stored in the WeeklyAggDates table has 5 values ( 2019-07-28, 2019-08-04, 2019-08-11, 2019-08-18 and 2019-08-25).

```dax
VAR MonthlyAggDates =
    SUMMARIZE (
        FILTER ( SummaryOfData, NOT ISBLANK ( [Monthly Agg Cover] ) ),
        [Monthly Agg Cover]
    )
VAR MonthlyDetailDates =
    SUMMARIZE (
        FILTER ( SummaryOfData, NOT ISBLANK ( [Monthly Detail from Day Table] ) ),
        [Monthly Detail from Day Table]
    )
VAR WeeklyAggDates =
    SUMMARIZE (
        FILTER ( SummaryOfData, NOT ISBLANK ( [Weekly Agg Cover] ) ),
        [Weekly Agg Cover]
    )
VAR WeeklyDetailDates =
    SUMMARIZE (
        FILTER ( SummaryOfData, NOT ISBLANK ( [Weekly Detail from Day Table] ) ),
        [Weekly Detail from Day Table]
    )

```

A decision can now be made if the week or month agg table should be used based on the row counts of these tables.

#### Step 6

The last step applies some criteria to decide what is required to return the final value. The IF statement at line 11 priorities in the following order

1. If the date range perfectly aligns with calendar months, only use the Monthly Agg table.
2. If the date range perfectly aligns with calendar weeks, only use the Weekly Agg table.
3. If there are fewer distinct base table dates required in the weekly method compared with monthly, use the weekly agg table + the overhang dates stored in the WeeklyDetailDates table.
4. Use values from the Monthly agg table (may not be any) + the overhang dates stored in the MonthlyDetailDates table.

```dax
VAR CountOfMonthlyAgg =
    COUNTROWS ( MonthlyAggDates )
VAR CountOfMontlyDetail =
    COUNTROWS ( MonthlyDetailDates )
VAR CountOFWeeklyAgg =
    COUNTROWS ( WeeklyAggDates )
VAR CountOfWeeklyDetail =
    COUNTROWS ( WeeklyDetailDates )
RETURN
    {
        IF (
            // All dates align with Calendar Month
            CountOfMonthlyAgg > 0
                &amp;&amp; CountOfMontlyDetail = 0,
            CALCULATE (
                [Events (Monthly)],
                TREATAS ( MonthlyAggDates, 'dim Date'[DateKey] )
            ),
            IF (
                // All dates align with Calendar Week
                CountOFWeeklyAgg > 0
                    &amp;&amp; CountOfWeeklyDetail = 0,
                CALCULATE ( [Events (Weekly)], TREATAS ( WeeklyAggDates, 'dim Date'[DateKey] ) ),
                IF (
                    //Gaps Exist to Horizontal Aggs to kick in
                    //If Weeks need fewer days, Use This
                    CountOfWeeklyDetail &lt; CountOfMontlyDetail,
                    CALCULATE ( [Events (Weekly)], TREATAS ( WeeklyAggDates, 'dim Date'[DateKey] ) )
                        + CALCULATE ( [Events], TREATAS ( WeeklyDetailDates, 'dim Date'[DateKey] ) ),
                    CALCULATE (
                        [Events (Monthly)],
                        TREATAS ( MonthlyAggDates, 'dim Date'[DateKey] )
                    )
                        + CALCULATE ( [Events], TREATAS ( MonthlyDetailDates, 'dim Date'[DateKey] ) )
                )
            )
        )
    }

```

You can download a [sample PBIX file](https://1drv.ms/u/s!AtDlC2rep7a-xwSUaBWYmJvIrkO7) here that includes all these steps in a single measure called [Horizontal Agg Measure]. The file lets you play with a date range slicer to compare the values between a measure that only uses the base table, with the Horizontal Agg measure.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B6-3.png?fit=1000%2C1019&ssl=1)

### Some timings

The following example shows results obtained on a real model. The model had the following characteristics:

| Table | Rows |
| --- | --- |
| Base Table | 13 Billion |
| Monthly Agg | 1.6 Billion |
| Weekly Agg | 4.5 Billion |

The results shown in the chart represent four test runs using a test harness running a set of 200 queries captured from user activity. Each batch was run three times and the cache was cleared prior to every query.

Each line in the chart represents a specific query and the Y-axis shows the time taken to run the query in milliseconds. The slowest query was in batch 1 and took slightly over 40 seconds to run.

1. Shows results of the test run on model with no aggs (no horizontal aggs)
2. Shows results of the test run on model with only monthly agg
3. Shows results of the test run on model with only weekly agg
4. Shows results of the test run on model with both monthly and weekly aggs, using horizontal aggs to generate results.

![](https://i2.wp.com/dax.tips/wp-content/uploads/2019/10/B7-3.png?fit=1000%2C472&ssl=1)

A few queries ran slower between the no-agg version at (1) and the monthly agg version at (2). These examples show queries where the overhead and additional work to get data from an agg table did not cancel out the saving. The rules I used at Step 6 can be tuned to, but you also need to keep your eye on the overall result.

#### Pros

This technique plays nicely with row-based Time Intelligence and works in most flavours of Analysis Services (SSAS, Azure AS, PowerBI Desktop). It also translates nicely to Direct Query data sources.

#### Cons

The downside is the additional 120 rows of DAX required to be included for each measure.

There is an overhead in the DAX to work out how to resolve the date range, but this is all down using small calendar tables and does not need to touch large FACT tables. In most cases, the overhead should be 30ms or less.

### Summary

In the right conditions, this technique can certainly make a positive difference to a model. These conditions include having lots of data along with requirements to report on date ranges that don’t always align with calendar weeks or months. It’s certainly not my “go-to” approach to when using aggregations, but I hope you recognise the creative nature of the design.

I would love to hear your feedback on this idea. Please feel free to contact me with questions or feedback.

Next up: [[creative-aggs-part-iii-accordion-aggs|Accordion Aggs]]

5
5
votes

Article Rating
