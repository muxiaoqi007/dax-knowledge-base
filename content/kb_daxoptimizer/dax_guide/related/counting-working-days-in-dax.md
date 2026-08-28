---
title: "Counting working days in DAX"
url: "https://www.sqlbi.com/articles/counting-working-days-in-dax"
published: "2020-08-17"
updated: 
source: "sqlbi.com"
---

# Counting working days in DAX

> 发布：2020-08-17

The example includes a Sales table containing order and delivery dates. DAX can compute the difference between two dates by subtracting one from the other. This produces the number of days between the two dates – a task that can be accomplished through a calculated column.

[![](https://cdn.sqlbi.com/wp-content/uploads/Counting-Working-Days-01.png)](https://cdn.sqlbi.com/wp-content/uploads/Counting-Working-Days-01.png)

How is it possible to compute the difference between the two dates, only computing working days and skipping weekends and holidays? Simple math is no longer useful here, and DAX does not offer a predefined function.

A solution to this scenario requires a date table – more details [here](https://www.sqlbi.com/articles/reference-date-table-in-dax-and-power-bi/) – with a specific column, IsWorkingDay, which indicates whether that particular day is a working day or not. The following figure shows an example:

[![](https://cdn.sqlbi.com/wp-content/uploads/Counting-Working-Days-02.png)](https://cdn.sqlbi.com/wp-content/uploads/Counting-Working-Days-02.png)

The IsWorkingDay column should be added to the existing Date table, otherwise it is necessary to create an ad-hoc date table for this purpose. In the demo file of this article, IsWorkingDay is computed by simply checking if the date is a Saturday or Sunday.

```dax


'Date'[IsWorkingDay] = NOT WEEKDAY( 'Date'[Date] ) IN { 1,7 }

```

More information about how to create working day columns are available in [this article](https://www.sqlbi.com/blog/alberto/2011/01/19/working-days-computation-in-powerpivot/).

Using the IsWorkingDay column, the Sales table can now include a new calculated column which writes as follows:

```dax


Sales[DeliveryWorkingDays] = 

CALCULATE(

    COUNTROWS ( 'Date'),

    DATESBETWEEN ( 'Date'[Date],  Sales[Order Date], Sales[Delivery Date] – 1 ),

    'Date'[IsWorkingDay] = TRUE,

    ALL ( Sales )

)

```

The function uses the [[DATESBETWEEN]] function, which returns a table with all the dates between the boundaries – Order Date and Delivery Date in the example. The result of [[DATESBETWEEN]] is further restricted by [[CALCULATE]], which applies the second filter to only consider working days.

Once the two filters are applied by [[CALCULATE]], the Date table specifically filters the working days between order and delivery. Then, the [[COUNTROWS]] function returns the number of working days in the DeliveryWorkingDay column, as shown in this final figure:

[![](https://cdn.sqlbi.com/wp-content/uploads/Counting-Working-Days-03.png)](https://cdn.sqlbi.com/wp-content/uploads/Counting-Working-Days-03.png)

[[DATESBETWEEN]]

Returns the dates between two given dates.

`DATESBETWEEN ( <Dates>, <StartDate>, <EndDate> )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[COUNTROWS]]

Counts the number of rows in a table.

`COUNTROWS ( [<Table>] )`

## Articles in the DAX 101 series

- [Computing running totals in DAX](https://www.sqlbi.com/articles/computing-running-totals-in-dax/)
- *Counting working days in DAX*
- [Summing values for the total](https://www.sqlbi.com/articles/summing-values-for-the-total/)
- [Year-to-date filtering weekdays in DAX](https://www.sqlbi.com/articles/year-to-date-filtering-weekdays-in-dax/)
- [Creating a simple date table in DAX](https://www.sqlbi.com/articles/creating-a-simple-date-table-in-dax/)
- [Automatic time intelligence in Power BI](https://www.sqlbi.com/articles/automatic-time-intelligence-in-power-bi/)
- [Using CONCATENATEX in measures](https://www.sqlbi.com/articles/using-concatenatex-in-measures/)
- [Previous year up to a certain date](https://www.sqlbi.com/articles/previous-year-up-to-a-certain-date/)
- [Sorting months in fiscal calendars](https://www.sqlbi.com/articles/sorting-months-in-fiscal-calendars/)
- [Using USERELATIONSHIP in DAX](https://www.sqlbi.com/articles/using-userelationship-in-dax/)
- [Mark as Date table](https://www.sqlbi.com/articles/mark-as-date-table/)
- [Row context in DAX](https://www.sqlbi.com/articles/row-context-in-dax/)
- [Filter context in DAX](https://www.sqlbi.com/articles/filter-context-in-dax/)
- [Introducing CALCULATE in DAX](https://www.sqlbi.com/articles/introducing-calculate-in-dax/)
- [Understanding context transition in DAX](https://www.sqlbi.com/articles/understanding-context-transition-in-dax/)
- [Using KEEPFILTERS in DAX](https://www.sqlbi.com/articles/using-keepfilters-in-dax-updated/)
- [Variables in DAX](https://www.sqlbi.com/articles/variables-in-dax/)
- [Using RELATED and RELATEDTABLE in DAX](https://www.sqlbi.com/articles/using-related-and-relatedtable-in-dax/)
- [Introducing ALLSELECTED in DAX](https://www.sqlbi.com/articles/introducing-allselected-in-dax/)
- [Introducing RANKX in DAX](https://www.sqlbi.com/articles/introducing-rankx-in-dax/)
