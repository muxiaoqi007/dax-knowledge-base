---
title: "Computing MTD, QTD, YTD in Power BI for the current period"
url: "https://www.sqlbi.com/articles/computing-mtd-qtd-ytd-in-power-bi-for-the-current-period"
published: "2023-11-06"
updated: 
source: "sqlbi.com"
---

# Computing MTD, QTD, YTD in Power BI for the current period

> 发布：2023-11-06

Time intelligence functions such as month-to-date (MTD), quarter-to-date (QTD), and year-to-date (YTD) in DAX operate relative to the current filter context. Their outcome depends on the filter applied, making them both adaptable for various periods and useful for comparisons. But, if you wish to showcase the most recent data – for the “current” period – there is a complication: without the proper filter, you may not get the data you aim for.

## Desired result visualization

For example, consider the following report: the values displayed at the month level in May 2019 report the MTD calculation starting from May 1, 2019. The same calculation is repeated every month. Because May 2019 belongs to the second quarter of 2019 (Q2), the QTD calculation starts on April 1, 2019, and the YTD calculation starts on January 1, 2019.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-68.png)

However, the last piece of data available in the sample used is for March 11, 2020, when the last delivery is available.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-65.png)

Thus, we can assume that March 11, 2020, is the last date available, or “yesterday”. A common requirement is a dashboard that shows the data for the “current” time period: the MTD calculation corresponds to the amount for the “current” month, the QTD calculation corresponds to the “current” quarter, and the YTD calculation corresponds to the “current” year.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-57.png)

You can see that the numbers reported in the first row of the card visual (all pertaining to *Sales Amount*) correspond to the values displayed in the previous screenshot with a matrix displaying the data for March 2020. The card visual (or, in some instances, the whole page) must be filtered using the last transaction date from the *Sales* table to present the desired results. To achieve this, we can apply a filter to the visual (or to the entire page) that filters only on March 3, 2020 – indeed, that is the date of the last transaction. However, a manual selection of that date would stop working if, at the next dataset refresh, the last date with data becomes March 12th or 13th. Thus, we should apply a “static” filter that works in the following days by automatically updating the effective filter applied to the visual.

## Setting the correct filter context

A common approach is to use a **Relative Date** slicer in Power BI. By setting “This Day” or “This Month”, you get a selection that appropriately applies the current date as a filter.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-51.png)

However, there are many limitations to this approach:

- By using “This Day”, you do not see any data for the current date if data is updated until the previous date. In that case, you might think of using “Last 2 Days”, but it does not work well on a Monday if there are no transactions over the weekend.
- By using “This Month”, you do not see any data the first day of the month if the model is refreshed every night – in that case, you might want to analyze the values of last month, because you do not have any data yet for the current month.
- By using “This Month”, you set a maximum date corresponding to the last day of the month. This filter might break a comparison of month-to-date over previous month-to-date, which should consider only 14 days in the previous month if the current month has only 14 days with data – unless you implemented the [proper DAX pattern](https://www.daxpatterns.com/standard-time-related-calculations/#computing-period-to-date-growth) calculation.

In general, selecting a date based on the [[TODAY]] function in DAX as a reference date and then applying a filter on the *Date[Date]* column will present the limitations described for the **Relative Date** slicer. Consider the start of a new month: if you are on the first day, you probably want data from the last day of the previous month. But not just any last day – you may specifically want the last working day or even the last day with transactions in the *Sales* table. This nuance underlines why we need a more refined approach.

For these reasons, we consider two possible approaches: one based on a calculated column in the *Date* table and the other based on a calculation group.

## Using a calculated column in the *Date* table

By creating a *CurrentDate* calculated column in the *Date* table, we can efficiently identify the latest date with transactional data with a specific string: **LastDateWithData**. We can apply a visual or page filter by selecting this value for the column. The LastDateWithData value is moved to the correct date at every refresh, becoming the correct reference for our time intelligence functions.

The *CurrentDate* calculated column relies on the *DateWithTransaction* column, which you automatically obtain in a *Date* table generated by Bravo for Power BI. If you are using another *Date* table, you can obtain the *DateWithTransaction* column by adding the following calculated column:

Calculated Column in Date table

```dax


DateWithTransactions = 

VAR __LastTransactionDate =

    MAXX (

        {

            MAX ( 'Sales'[Order Date] ),

            MAX ( 'Sales'[Delivery Date] )

        },

        [Value]

    )

RETURN

    'Date'[Date] <= __LastTransactionDate

```

The formula for the *CurrentDate* calculated column is:

Calculated Column in Date table

```dax


CurrentDate =

VAR LastDateWithData =

    CALCULATE (

        MAX ( 'Date'[Date] ),

        'Date'[DateWithTransactions] = TRUE,

        REMOVEFILTERS ()

    )

RETURN

    IF (

        LastDateWithData == 'Date'[Date],

        "LastDateWithData"

    )

```

The *LastDateWithData* variable retrieves the maximum date (**[[MAX]] ( ‘Date'[Date] )**) where there are transactions (**‘Date'[DateWithTransactions] = TRUE**). The [[REMOVEFILTERS]] function ensures that this calculation is done over the entirety of the *Date* table, ignoring the context transition due to the [[CALCULATE]] function in the calculated column.

Once the *LastDateWithData* variable holds the date of the last transaction, the RETURN statement checks each row of the *Date* table to see if its date matches the *LastDateWithData*. If there is a match, it tags that row with “LastDateWithData”. Essentially, this calculated column acts as a flag that indicates which date in the *Date* table corresponds to the most recent transaction date.

Having this calculated column in the *Date* table simplifies many subsequent operations. For instance, when you want to filter a visual to only show data up to the most recent transaction date, you filter by the “LastDateWithData” value in the *CurrentDate* column. We can apply this filter in the filter pane to a visual, all the visuals in a page, or the entire report.

![](https://cdn.sqlbi.com/wp-content/uploads/image5-43.png)

This filter ensures that MTD, QTD, and YTD calculations always reflect values up to the latest transaction, automatically considering the latest period available in the data.

## Using a calculation group

A calculation group can intercept all the measures and apply the same filter as we did by filtering a calculated column on *Date* in the previous example. While the calculation group could be a wrapper over the calculated column we defined before, we can also implement the entire business logic within the calculation item so that the calculation group does not depend on additional changes made to the data model.

In Power BI Desktop, we can create a calculation group by using the **Calculation group** button in the Calculations sections of the ribbon visible when we activate the Model view.

![](https://cdn.sqlbi.com/wp-content/uploads/image6-38.png)

We create a *Current Period* calculation group using the same name (*Current Period*) for the column with the list of calculation items. The first calculation item we define is *Last date with data*:

Calculation Item in Current Period table

```dax


Last date with data = 

VAR LastDateWithData =

    CALCULATE (

        MAX (

            MAX ( Sales[Order Date] ),

            MAX ( Sales[Delivery Date] )

        ),

        REMOVEFILTERS ()

    )

RETURN

    CALCULATE (

        SELECTEDMEASURE (),

        'Date'[Date] = LastDateWithData

    )

```

The *LastDateWithData* variable retrieves the maximum date present in *Sales[Order Date]* and *Sales[Delivery Date]* when there are transactions (**‘Date'[DateWithTransactions] = TRUE**). The [[REMOVEFILTERS]] function ensures the calculation considers all rows in *Sales*, regardless of any external filter.

With the most recent transaction date identified, the formula then recalculates the currently selected measure ([[SELECTEDMEASURE]]) under the context of this date. When you apply this calculation item as a filter to a visual, all the measures use the last date with transactions as a filter context, so YTD, QTD, and MTD use that date as a reference.

![](https://cdn.sqlbi.com/wp-content/uploads/image7-30.png)

The calculation group can also have additional calculation items for different selections. For example, *Last month with data* selects the entire month that includes the last date with data:

Calculation Item in Current Period table

```dax
]

Last month with data = 

VAR LastDateWithData =

    CALCULATE (

        MAX (

            MAX ( Sales[Order Date] ),

            MAX ( Sales[Delivery Date] )

        ),

        REMOVEFILTERS ()

    )

VAR LastMonthWithData =

    LOOKUPVALUE ( 

        'Date'[Year Month Number],

        'Date'[Date], LastDateWithData

    )

RETURN

    CALCULATE (

        SELECTEDMEASURE (),

        'Date'[Year Month Number] = LastMonthWithData,

        REMOVEFILTERS ( 'Date' ) 

    )

```

The [[REMOVEFILTERS]] is necessary because we apply a filter on a column other than *Date[Date]*, which triggers an automatic filter removal when filtered.

If you do not have a *Year Month Number* column in the *Date* table, you can implement this more generic version that relies only on the *Date* table:

Calculation Item in Current Period table

```dax


Last month with data (no Date dependencies) = 

VAR LastDateWithData =

    CALCULATE (

        MAX (

            MAX ( Sales[Order Date] ),

            MAX ( Sales[Delivery Date] )

        ),

        REMOVEFILTERS ()

    )

RETURN

    CALCULATE (

        SELECTEDMEASURE (),

        PARALLELPERIOD (

            TREATAS (

                { LastDateWithData },

                'Date'[Date]

            ),

            0,

            MONTH

        )

    )

```

Similarly, we can implement the *last quarter with data* calculation item by relying on the *Date[Year Quarter Number]* column:

Calculation Item in Current Period table

```dax


Last quarter with data = 

VAR LastDateWithData =

    CALCULATE (

        MAX (

            MAX ( Sales[Order Date] ),

            MAX ( Sales[Delivery Date] )

        ),

        REMOVEFILTERS ()

    )

VAR LastQuarterWithData =

    LOOKUPVALUE ( 

        'Date'[Year Quarter Number],

        'Date'[Date], LastDateWithData

    )

RETURN

    CALCULATE (

        SELECTEDMEASURE (),

        'Date'[Year Quarter Number] = LastQuarterWithData

    )

```

The version that does not rely on specific columns in the *Date* table uses **[[QUARTER]]** instead of **[[MONTH]]** in the [[PARALLELPERIOD]] function:

Calculation Item in Current Period table

```dax


Last quarter with data (no Date dependencies) = 

VAR LastDateWithData =

    CALCULATE (

        MAX (

            MAX ( Sales[Order Date] ),

            MAX ( Sales[Delivery Date] )

        ),

        REMOVEFILTERS ()

    )

RETURN

    CALCULATE (

        SELECTEDMEASURE (),

        PARALLELPERIOD (

            TREATAS (

                { LastDateWithData },

                'Date'[Date]

            ),

            0,

            QUARTER

        )

    )

```

## Conclusions

While time intelligence functions offer flexibility and power in DAX, tailoring them for the latest period can pose a challenge. One can use a combination of filter context manipulations and a calculated column or a calculation group to overcome this.

By implementing these strategies, you can use the same time intelligence calculations for both time-related reports and dashboards that display the available information related to the more recent time period.

[[TODAY]]

Returns the current date in datetime format.

`TODAY ( )`

[[MAX]]

Returns the largest value in a column, or the larger value between two scalar expressions. Ignores logical values. Strings are compared according to alphabetical order.

`MAX ( <ColumnNameOrScalar1> [, <Scalar2>] )`

[[REMOVEFILTERS]]

CALCULATE modifier

Clear filters from the specified tables or columns.

`REMOVEFILTERS ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[SELECTEDMEASURE]]

Context transition

Returns the measure that is currently being evaluated.

`SELECTEDMEASURE ( )`

[[QUARTER]]

Returns a number from 1 (January-March) to 4 (October-December) representing the quarter.

`QUARTER ( <Date> )`

[[MONTH]]

Returns a number from 1 (January) to 12 (December) representing the month.

`MONTH ( <Date> )`

[[PARALLELPERIOD]]

Context transition

Returns a parallel period of dates by the given set of dates and a specified interval.

`PARALLELPERIOD ( <Dates>, <NumberOfIntervals>, <Interval> )`
