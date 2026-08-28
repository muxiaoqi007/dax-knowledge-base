---
title: "Filtering weekdays in DAX"
url: "https://www.sqlbi.com/articles/filtering-weekdays-in-dax"
published: "2025-06-17"
updated: 
source: "sqlbi.com"
---

# Filtering weekdays in DAX

> 发布：2025-06-17

Computing time intelligence calculations in DAX is rather simple. However, as soon as the requirements are not trivial, the complexity of formulas skyrockets, and it is necessary to have a very good understanding of several details about DAX to obtain a good formula. In this article, we show a simple requirement: the need to maintain a filter on weekdays while computing time intelligence. As you are about to read, it will require several complex steps despite being a simple requirement; but let us start by clarifying what we want to obtain and what a filter-preserving column is.

The *Day of Week* column should be considered a filter-preserving column: a column that will maintain the filter selection (on that column) regardless of filters being applied to other columns. For example, a filter on *Day of Week* should be maintained regardless of a filter over *Month*. In a *Date* table there are columns (like *Month*) that are not filter-preserving columns because they are combined with other filters (like *Year*) and they produce an effect in time intelligence calculations by considering the combination of the filters (like *Year* and *Month*). The *Day of Week* filter is a filter that should not affect the time intelligence calculation.

For example, consider the “previous month” calculation over the “Monday to Friday, January, 2020” selection: the goal is to obtain a result that is “Monday to Friday, December, 2019”. As you see, *Month* and *Year* can change because of the filter (technically, those filters are removed and replaced by a filter with days from Dec. 1, 2019 to Dec. 31, 2019), while the *Day of Week* filter is not touched by the time intelligence calculations. In the end, the actual filter is “Monday to Friday (this *Day of Week* column is filter-preserving), Dec. 1, 2019 to Dec. 31, 2019 (the filter on *Date* replaces the filter on *Month* and *Year* after the time intelligence calculation).

The final formula in this article is short and sweet. However, the code contains many small details, each of which is very relevant. Besides, an important takeaway of the article is that it shows how to rely on [[KEEPFILTERS]] to avoid the automatic [[REMOVEFILTERS]] executed by DAX when time intelligence functions are used.

For the laziest among our readers, here is the code we will write at the end:

Measure in Sales table

```dax


Sales PM = 

CALCULATE (

    [Sales Amount],

    ALLEXCEPT ( 'Date', 'Date'[Day of Week Number], 'Date'[Day of Week] ),

    KEEPFILTERS ( 

        CALCULATETABLE (

            DATEADD ( 'Date'[Date], -1, MONTH ),

            REMOVEFILTERS ( 'Date'[Day of Week Number], 'Date'[Day of Week] )    

        ) 

    )

 )

```

It is only 10 lines of code. You can copy & paste in your models, but we urge you not to. Take your time to read the article and follow each step to make sense of all the details. It may look boring, and it may be boring at some point. However, details are essential; we do not want you to miss any. The most interesting line of the formula is [[KEEPFILTERS]] around the second argument of [[CALCULATE]]. It is sexy and dangerous. Tempting but scary. We will need to tame it and deeply understand what its meaning is, along with the rest of the formula.

Enough with the disclaimers; let us start with the real content!

If one wants to compare the current month with the previous month, the simplest way to reach that goal is to use a formula like the following:

Measure in Sales table

```dax


Sales PM =

CALCULATE ( 

    [Sales Amount], 

    DATEADD ( 'Date'[Date], -1, MONTH ) 

)

```

This formula works just fine; once used in a matrix, it produces the correct result.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-104.png)

Does it seem easy? It is. However, it looks simple just because several small features silently work together to make this code work.

[[DATEADD]] returns a table containing the *Date[Date]* column. The table includes all the dates of the previous month. The filter on *Date[Date]* overrides neither the filter on *Date[Year]* nor the filter on *Date[Month]* that is present in the matrix. However, when the relationship between *Sales* and *Date* is based on a column of Date type, DAX automatically adds [[REMOVEFILTERS]] whenever a new filter is applied to *Date[Date]*. Hence, [[DATESYTD]] indirectly adds [[REMOVEFILTERS]] to *Date* because the relationship between *Sales* and *Date* is based on a column (*Sales[Order Date]*) that is a DateTime column. If the relationship is based on an integer or any other data type, it is necessary to mark the *Date* table as a date table to achieve the same goal.

This automatic behavior is very welcome in most scenarios. Yet, it proves to be problematic when the *Date* table contains columns for which we want to keep the filter when we apply a time intelligence transformation.

As an example, look at what happens if we add a slicer that filters *Date[Day of Week]*, filtering only working days.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-101.png)

You can quickly notice that the value reported in *Sales PM* is no longer the value of *Sales Amount* in the previous month. To better understand what is happening, let us filter one weekday only and expand the date.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-93.png)

The dates selected for August 2021 are all the Mondays in August 2021. This selection of dates is shifted back by [[DATEADD]], resulting in the same dates in July 2021. In other words, for the 23rd of August, *Sales PM* returns 2,876.57, which is the *Sales Amount* of the 23rd of July.

The value computed by *Sales PM* makes sense, but it is hardly what a user would want. The intention of the users is likely to compare a selection of days of the week in a month with the same selection of days of the week in the previous month. In other words, they want to compare Mondays in August against Mondays in July.

What is happening is the following: the filter on *Date[Day of Week]* determines a selection of dates, which is then moved back to the previous month. What we want to obtain is the opposite: first, move the month selection back to the previous month, then apply the filter on *Date[Day of Week]*. By following the steps in this order, we obtain a behavior that is the one expected by our users.

With this idea in mind, we can write the first trial, which turns out wrong. We will refine the code and the model in several steps to showcase the challenges one by one:

Measure in Sales table

```dax


Sales PM = 

CALCULATE (

    [Sales Amount],

    --

    --  Compute the previous month after removing the filter over the day

    --  of the week to retrieve the full previous month

    --

    CALCULATETABLE (

        DATEADD ( 'Date'[Date], -1, MONTH ),

        REMOVEFILTERS ( 'Date'[Day of Week Number], 'Date'[Day of Week] )    

    ),

    --

    --  Apply the filter over the day of the week again, because

    --  the DATEADD filter removes all the filters

    --

    VALUES ( 'Date'[Day of Week] )

)

```

At first sight, this measure works fine because at the month level, the results are as expected.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-87.png)

However, if one expands the matrix at the day level, there is a serious issue: all the values are blank, despite the total showing a correct number.

![](https://cdn.sqlbi.com/wp-content/uploads/image5-77.png)

Here is the problem: at the day level, *[[VALUES]] ( ‘Date'[Day of Week] )* returns the given day of the week. For example, on the fifth of August 2021, it returns Wednesday. [[DATEADD]] returns a table containing the fifth of July, which happens to be a Monday. Hence, the filter for the day of the week removes the fifth of July as a valid value, making the formula return blank everywhere. You can double-check this by looking at data from March 2022. Because the previous month is February, which contains a round number of weeks, the day of the week of dates in March is the same day of the week of the corresponding date in February, making the formula return good values.

![](https://cdn.sqlbi.com/wp-content/uploads/image6-70.png)

The problem with using [[VALUES]] is that it returns the visible values of a column by considering cross-filtering. The filter on *Date[Date]* cross-filters *Date[Day of Week]*; this is why *[[VALUES]] ( ‘Date'[Day of Week] )* returns the day of the week of the current date only. DAX offers the [[FILTERS]] function, which behaves the same as [[VALUES]], with the critical difference that [[FILTERS]] ignores cross-filtering. Hence, replacing [[VALUES]] with [[FILTERS]] makes the code work, even though the following is not the final formula yet:

Measure in Sales table

```dax


Sales PM = 

CALCULATE (

    [Sales Amount],

    --

    --  Compute the previous month after removing the filter over the day

    --  of the week to retrieve the full previous month

    --

    CALCULATETABLE (

        DATEADD ( 'Date'[Date], -1, MONTH ),

        REMOVEFILTERS ( 'Date'[Day of Week Number], 'Date'[Day of Week] )    

    ),

    --

    --  Apply the filter over the day of the week again, because

    --  the DATEADD filter removes all the filters. We use FILTERS

    --  to ignore cross-filtering on 'Date'[Day of Week]

    --

    FILTERS ( 'Date'[Day of Week] )

)

```

This is the first working version of the formula. It relies on the automatic [[REMOVEFILTERS]] executed by DAX (please note that we are not removing the filters from *Date[Year]* and Date*[Month]*) and it restores the filter on the day of the week.

This code is not the best option because the table returned by [[FILTERS]] always needs to be applied as an additional filter, even if no filters are active. When no filtering happens on the day of the week, [[FILTERS]] still returns seven values. The table needs to be used as a filter, and this operation, despite being simple, requires time. Hence, the code is not optimal.

DAX offers the [[ALLEXCEPT]] function, which aims to remove all filters from a table except for filters that may be active on a selection of columns. Hence, we can rely on [[ALLEXCEPT]] rather than [[FILTERS]] to restore the filter on the day of the week. If no filters are active, [[ALLEXCEPT]] will not do anything, whereas if some filters are active, they will all be removed except the ones on the day of the week. Unfortunately, the following version of the code suffers from a few issues, as it does not work:

Measure in Sales table

```dax


Sales PM = 

CALCULATE (

    [Sales Amount],

    --

    --  Compute the previous month after removing the filter over the day

    --  of the week to retrieve the full previous month

    --

    CALCULATETABLE (

        DATEADD ( 'Date'[Date], -1, MONTH ),

        REMOVEFILTERS ( 'Date'[Day of Week Number], 'Date'[Day of Week] )    

    ),

    --

    --  Removes all the filters from Date, except the ones on the

    --  day of the week, if present

    --

    ALLEXCEPT ( 'Date', 'Date'[Day of Week Number], 'Date'[Day of Week] )

)

```

When used in a matrix, it always returns the full previous month, as if no filter were being kept on the day of the week.

![](https://cdn.sqlbi.com/wp-content/uploads/image7-59.png)

Despite being a step in the right direction, the formula still does not work because DAX executes an automatic [[REMOVEFILTERS]]. [[ALLEXCEPT]] removes all the filters from the *Date* table (including the *Date[Date]* column), keeping only the filter on the day of the week. However, when the result of [[DATEADD]] is added to the filter context, DAX executes an automatic [[REMOVEFILTERS]] on *Date*, vanishing the effect of [[ALLEXCEPT]].

The last touch is to recognize that [[ALLEXCEPT]] is a [[CALCULATE]] modifier. It is executed before applying the explicit filter generated by CALCULATETABLE/DATEADD to the filter context. Therefore, the [[KEEPFILTERS]] modifier around [[CALCULATETABLE]] is enough to ensure that the filter on *Date[Date]* does not remove any previously-existing filter. The last filter on *Date* was applied by [[ALLEXCEPT]], which removed any filter (except on the day of the week).

Here is the final formula. Despite it being unnecessary, we switched the order of the two filters in [[CALCULATE]] because – as humans – we find it easier to read [[ALLEXCEPT]] first (as it is executed first) and KEEPFILTERS/CALCULATETABLE/DATEADD next. Be mindful that the order is not relevant. The execution order of the filters is defined in the rules of [[CALCULATE]], not in the order in which we provide the parameters:

Measure in Sales table

```dax


Sales PM = 

CALCULATE (

    [Sales Amount],

    --

    --  Removes all the filters from Date, except the ones on the

    --  day of the week, if present

    --

    ALLEXCEPT ( 'Date', 'Date'[Day of Week Number], 'Date'[Day of Week] ),

    --

    --  Compute the previous month after removing the filter over the day

    --  of the week, to retrieve the full previous month.

    --  KEEPFILTERS prevents the automatic REMOVEFILTERS added by DAX

    --  and it makes sure that the previous ALLEXCEPT maintains the

    --  filter on the day of the week

    --

    KEEPFILTERS ( 

        CALCULATETABLE (

            DATEADD ( 'Date'[Date], -1, MONTH ),

            REMOVEFILTERS ( 'Date'[Day of Week Number], 'Date'[Day of Week] )    

        ) 

    )

)

```

This final formula works. As you have learned from reading the article, each line of the formula has a deep meaning, and changing any detail may break the code. We showed this last version of the formula at the beginning of the article, but the details are crucial. Once you deeply understand these details, you can adapt or change the code for your model, knowing the effect of any small change.

## Conclusions

As you have seen, a simple requirement like maintaining some filters on *Date* with time intelligence calculations proves to be a real challenge in DAX. DAX offers several automatic behaviors whose goal is to simplify formula writing. However, when these mechanisms are not desired, it is necessary to roll up your sleeves, remind yourself about all the important and subtle details of DAX, and then author the code.

The code is not trivial, and one could object that writing all this code every time they need to perform a simple time intelligence calculation would be overkill. It would be great to define a function that performs all these steps so you can invoke that function in every measure that needs a time intelligence calculation. This is the topic for a future article, so stay tuned!

[[KEEPFILTERS]]

CALCULATE modifier

Changes the CALCULATE and CALCULATETABLE function filtering semantics.

`KEEPFILTERS ( <Expression> )`

[[REMOVEFILTERS]]

CALCULATE modifier

Clear filters from the specified tables or columns.

`REMOVEFILTERS ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[DATEADD]]

Context transition

Moves the given set of dates by a specified interval.

`DATEADD ( <Dates>, <NumberOfIntervals>, <Interval> [, <Extension>] [, <Truncation>] )`

[[DATESYTD]]

Context transition

Returns a set of dates in the year up to the last date visible in the filter context.

`DATESYTD ( <Dates> [, <YearEndDate>] )`

[[VALUES]]

When a column name is given, returns a single-column table of unique values. When a table name is given, returns a table with the same columns and all the rows of the table (including duplicates) with the [additional blank row](https://www.sqlbi.com/articles/blank-row-in-dax/) caused by an invalid relationship if present.

`VALUES ( <TableNameOrColumnName> )`

[[FILTERS]]

Returns a table of the filter values applied directly to the specified column.

`FILTERS ( <ColumnName> )`

[[ALLEXCEPT]]

CALCULATE modifier

Returns all the rows in a table except for those rows that are affected by the specified column filters.

`ALLEXCEPT ( <TableName>, <ColumnName> [, <ColumnName> [, … ] ] )`

[[CALCULATETABLE]]

Context transition

Evaluates a table expression in a context modified by filters.

`CALCULATETABLE ( <Table> [, <Filter> [, <Filter> [, … ] ] ] )`
