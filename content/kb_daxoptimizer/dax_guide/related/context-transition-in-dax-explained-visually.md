---
title: "Context transition in DAX explained visually"
url: "https://www.sqlbi.com/articles/context-transition-in-dax-explained-visually"
published: "2024-09-09"
updated: 
source: "sqlbi.com"
---

# Context transition in DAX explained visually

> 发布：2024-09-09

In previous articles, we introduced a visual approach to describing two important DAX concepts: the [filter context](https://www.sqlbi.com/articles/filter-context-in-dax-explained-visually/) and the [row context](https://www.sqlbi.com/articles/row-context-in-dax-explained-visually/). This article completes this short series by describing the context transition using a graphical visualization.

This article provides a different perspective on the [context transition](https://www.sqlbi.com/topics/context-transition/) already covered in other articles: you should read them to get more insights on this important concept for DAX.

## Definition of context transition

The context transition **transforms** any active **row context** into a corresponding filter in the **filter context**. The context transition is executed by [[CALCULATE]] and [[CALCULATETABLE]], and by any DAX syntax that implicitly invokes one of these two functions. For example, any measure reference implies [[CALCULATE]], which invokes the context transition. There are also several other DAX functions that internally involve [[CALCULATE]] or [[CALCULATETABLE]] performing a context transition: these functions are marked by the context transition tag in [DAX Guide](https://dax.guide/), like for example [[LASTDATE]].

![](https://cdn.sqlbi.com/wp-content/uploads/image1-89.png)

You can filter the Context transition group on DAX Guide to get a list of all the functions that involve CALCULATE/CALCULATETABLE and execute a context transition if they are invoked with an active row context.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-86.png)

To execute a context transition, there must be an active row context. Therefore, any context transition occurs in calculated columns and within DAX iterators like [[FILTER]] and [[SUMX]]. You can find a complete list of iterator functions by filtering the Iterator group in DAX Guide.

Besides the formal definition, let us try to describe the context transition with an example. When you iterate a list of products, each product has a row context; the context transition filters the “current” product in the filter context, so any aggregation or table expression is filtered by that product. Still unclear? We can try with a visual representation!

## Transform the row context into a filter

Consider the following report, which shows the *Sales Amount* and *Average Customer Sales* measures for each product brand.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-78.png)

The *Average Customer Sales* measure computes *Sales Amount* for every customer and returns the average of the non-blank values:

Measure in Sales table

```dax


Average Customer Sales = 

AVERAGEX (

    Customer,

    [Sales Amount]

)

```

The value of *Average Customer Sales* for Contoso is 786.18, and it is calculated this way:

1. The filter context has only one filter, Contoso, for the *Brand*
2. In this filter context, the first argument of [[AVERAGEX]] (*Customer*) is evaluated, and it returns all the *Customer* rows because there are no filters affecting *Customer*.
3. [[AVERAGEX]] evaluates the second argument in a row context for each row of *Customer*:
4. The *Sales Amount* measure reference applies the context transition to the row context.
5. The filter context to evaluate *Sales Amount* inherits the initial filter context (*Brand* is Contoso) and adds the filter obtained by the context transition.
6. *Sales Amount* is then computed in the filter context resulting from the context transition.
7. [[AVERAGEX]] computes the average of the results obtained, ignoring blank values.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-72.png)

In other words, the iteration ([[AVERAGEX]]) produces a row context for each customer; the *Sales Amount* measure reference implies [[CALCULATE]], which transforms each row context into a filter context. In practice, each customer has a separate filter context to compute the *Sales Amount* of that customer; each filter context is formed by the initial filter context of the report that invokes the measure, plus the filter on the customer produced by the context transition.

Every iterator would behave similarly; the only difference between them is the final aggregation applied to the results obtained for each row: [[AVERAGEX]] computes an average of the results, whereas [[SUMX]] sums the results.

## Remove the effects of the context transition

There are cases where you want to remove the effects of the context transition. For example, consider the following report, which shows in *Sales Top Customers* the sales to customers worth at least 1% of the revenues for the selected brand.

![](https://cdn.sqlbi.com/wp-content/uploads/image5-63.png)

For educational purposes, we write the following measure, which computes the sales of all the customers within the iterator. This is not ideal for performance, but we wanted a relatively simple example to show how to remove the effects of the context transition if required:

Measure in Sales table

```dax


Sales Top Customers = 

SUMX (

    Customer,

    VAR SalesCustomer = [Sales Amount]

    VAR SalesAllCustomers =

        CALCULATE (

            [Sales Amount],

            REMOVEFILTERS ( Customer )

        )

    VAR Result =

        IF (

            SalesCustomer > SalesAllCustomers * 0.01,

            [Sales Amount]

        )

    RETURN Result

)

```

The *SalesAllCustomers* variable must have the total amount of sales for all the customers in the current filter context. The *Sales Amount* measure performs a context transition, which is what we need for the *SalesCustomer* variable. For *SalesAllCustomers*, we should remove the row context before executing the *Sales Amount* measure – but this is not possible in a direct way. What we can do is perform a context transition with an explicit [[CALCULATE]], and then use [[REMOVEFILTERS]] to remove the filter on *Customer* added by the context transition. Once the filter generated by the context transition is removed, we no longer have either the row context or the corresponding filter context obtained by the context transition.

![](https://cdn.sqlbi.com/wp-content/uploads/image6-56.png)

[[REMOVEFILTERS]] is evaluated after the context transition and before other filters, according to the [[CALCULATE]] evaluation order. As we mentioned, we created this example for educational purposes because the need to remove the context transition may arise in more complex scenarios. In this case, a better solution would have been to compute the *SalesAllCustomers* variable before the [[SUMX]] iteration:

Measure in Sales table

```dax


Sales Top Customers Optimized = 

VAR SalesAllCustomers = [Sales Amount]

RETURN 

    SUMX (

        Customer,

        VAR SalesCustomer = [Sales Amount]

        VAR Result =

            IF (

                SalesCustomer > SalesAllCustomers * 0.01,

                SalesCustomer 

            )

        RETURN Result

    )

```

## Maintain filters affected by context transition

The filter created by the context transition overwrites any existing filter on the same columns. This is the default behavior of the filter context. When we want to preserve the existing filters in [[CALCULATE]], we use [[KEEPFILTERS]] around the filter arguments that should not remove existing filters. However, the context transition does not correspond to a [[CALCULATE]] filter argument; rather, it is a hidden behavior that affects the row context generated by an iterator function. Therefore, we need a particular syntax for [[KEEPFILTERS]] in that case, which we introduce with an example.

Consider the following report showing a slicer selection with November and December in 2018 and January and February in 2019.

![](https://cdn.sqlbi.com/wp-content/uploads/image7-46.png)

The measure required is a monthly average of sales, which we initially write as *Monthly Average Incorrect*:

Measure in Sales table

```dax


Monthly Average Incorrect = 

AVERAGEX ( 

    DISTINCT ( 'Date'[Month] ),

    [Sales Amount]

)

```

The measure returns an incorrect value for the total row, which should be the average for the four months. Instead, it shows a value larger than any displayed number. Something is clearly not working as expected. The analysis of the filter context and of the context transition provides an explanation of the incorrect result: the filter context for the total row has a filter with two columns, *Year* and *Month*.

![](https://cdn.sqlbi.com/wp-content/uploads/image8-39.png)

The filter has an arbitrary selection of combinations between years and months. These filters are also called “arbitrary-shaped filters” because they do not correspond to all the possible or existing combinations of values in two or more columns. However, the relevant information is that the filter has two columns.

The [[AVERAGEX]] function iterates a table that has only one column, *Month*. Therefore, the row context for each month includes only the *Month* column, and the filter obtained by the context transition and applied to the filter context has only the *Month* column. This new filter removes any existing filter on *Month*. However, because the existing filter has *Year* and *Month*, the Year column of the existing filter is maintained.

![](https://cdn.sqlbi.com/wp-content/uploads/image9-35.png)

The filter context that evaluates the *Sales Amount* measure for October has one month (October) and two years (2018, 2019).

![](https://cdn.sqlbi.com/wp-content/uploads/image10-30.png)

This process is repeated every month. Therefore, there are four months iterated, but for each month *Sales Amount* returns the sum of 2018 and 2019 filtered by that month. The Total column in the following screenshot explains the result obtained by summing two years for each month; the Total row shows the corresponding value of the monthly average.

![](https://cdn.sqlbi.com/wp-content/uploads/image11-21.png)

To get the correct result, we want to apply [[KEEPFILTERS]] to the filter added by the context transition. The syntax requires applying [[KEEPFILTERS]] over the iterator argument of the DAX iterator function. When [[KEEPFILTERS]] is applied to an iterator, that [[KEEPFILTERS]] is applied to any context transition generated on the row context produced by that iterator. In this case, [[KEEPFILTERS]] must wrap around the *[[DISTINCT]] ( Date[Month] )* argument of [[SUMX]]:

Measure in Sales table

```dax


Monthly Average = 

AVERAGEX ( 

    KEEPFILTERS (

        DISTINCT ( 'Date'[Month] )

    ),

    [Sales Amount]

)

```

The presence of [[KEEPFILTERS]] stops the context transition on *Month* from removing part of the existing filters on *Year-Month*.

![](https://cdn.sqlbi.com/wp-content/uploads/image12-13.png)

It is worth noting that the [[KEEPFILTERS]] would not have been required if the iterator were *Date[Year Month]* instead of *Date[Month]*. However, discussing the best practices about using [[KEEPFILTERS]] is outside of the goal of this article.

## Conclusions

The context transition transforms a row context into a corresponding filter in the filter context. The visual representation clarifies that the row context is like a table with only one row that is moved into the filter context. Like any other filters, the row context overwrites existing filters over the same columns unless [[KEEPFILTERS]] is applied to the iterator.

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[CALCULATETABLE]]

Context transition

Evaluates a table expression in a context modified by filters.

`CALCULATETABLE ( <Table> [, <Filter> [, <Filter> [, … ] ] ] )`

[[LASTDATE]]

Context transition

Returns last non blank date.

`LASTDATE ( <Dates> )`

[[FILTER]]

Returns a table that has been filtered.

`FILTER ( <Table>, <FilterExpression> )`

[[SUMX]]

Returns the sum of an expression evaluated for each row in a table.

`SUMX ( <Table>, <Expression> )`

[[AVERAGEX]]

Calculates the average (arithmetic mean) of a set of expressions evaluated over a table.

`AVERAGEX ( <Table>, <Expression> )`

[[REMOVEFILTERS]]

CALCULATE modifier

Clear filters from the specified tables or columns.

`REMOVEFILTERS ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[KEEPFILTERS]]

CALCULATE modifier

Changes the CALCULATE and CALCULATETABLE function filtering semantics.

`KEEPFILTERS ( <Expression> )`

[[DISTINCT]]

Returns a one column table that contains the distinct (unique) values in a column, for a column argument. Or multiple columns with distinct (unique) combination of values, for a table expression argument.

`DISTINCT ( <ColumnNameOrTableExpr> )`
