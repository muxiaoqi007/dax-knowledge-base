---
title: "Computing running totals in DAX"
url: "https://www.sqlbi.com/articles/computing-running-totals-in-dax"
published: "2020-08-17"
updated: 
source: "sqlbi.com"
---

# Computing running totals in DAX

> 发布：2020-08-17

A very common calculation in DAX is the year-to-date calculation (YTD), which aggregates values from the beginning of the year all the way to a certain date. A simple implementation uses the predefined [[DATESYTD]] function:

```dax


Sales YTD := 

CALCULATE ( 

    [Sales Amount], 

    DATESYTD( 'Date'[Date] ) 

)

```

For each month, this returns the aggregated value of all sales in that month plus all previous months within the same calendar year:

[![](https://cdn.sqlbi.com/wp-content/uploads/Computing-running-total-01.png)](https://cdn.sqlbi.com/wp-content/uploads/Computing-running-total-01.png)

[[DATESYTD]] resets every year. If the goal is to sum values over more than one year, then [[DATESYTD]] is no longer useful. In that case, the calculation requires an explicit filter in plain DAX.  
The computation of the running total requires a filter that retrieves all the dates prior to the current date in the filter context. Here is a simple way to obtain this:

```dax


Sales RT := 

VAR MaxDate = MAX ( 'Date'[Date] ) -- Saves the last visible date

RETURN

    CALCULATE ( 

        [Sales Amount],            -- Computes sales amount

        'Date'[Date] <= MaxDate,   -- Where date is before the last visible date

        ALL ( Date )               -- Removes any other filters from Date

    )

```

First, the MaxDate variable saves the last visible date. Then, two [[CALCULATE]] filters remove all the filters on the Date table and they replace the filter on the Date column showing all the dates prior to MaxDate.  
The figure below shows the difference between year-to-date which resets at year end, and running totals that carry into the new year.

[![](https://cdn.sqlbi.com/wp-content/uploads/Computing-running-total-02.png)](https://cdn.sqlbi.com/wp-content/uploads/Computing-running-total-02.png)

If the column used in the relationship between Date and Sales is a DateTime data type, or if the Date table is marked as a date table, then the [[ALL]] ( Date ) statement is not required because it is automatically added by the engine. Nevertheless, the pattern includes that filter for clarity’s sake, so that it can be used even if the relationship does not use a DateTime data type column.  
A similar technique can show running totals over different attributes and dimensions. In the demo database, customers are clustered into different categories, based on purchase volume: Platinum, Gold and Silver. What if the user wants a running total of sales amounts for their top tier customers, starting at the top with the Platinum category? The running total pattern is a useful technique here, too.  
The goal is to obtain the following report: :

[![](https://cdn.sqlbi.com/wp-content/uploads/Computing-running-total-03.png)](https://cdn.sqlbi.com/wp-content/uploads/Computing-running-total-03.png)

The DAX code for RT Sales Customer Class uses the very same pattern as for the running total described earlier:

```dax


RT Sales Customer Class := 

VAR CurrentCustomerClass = SELECTEDVALUE ( Customer[Customer Class Number] )

RETURN

    CALCULATE ( 

        [Sales Amount], 

        Customer[Customer Class Number] <= CurrentCustomerClass, 

        ALL ( Customer[Customer Class] ) 

    )

```

The running total pattern over tables other than Date is useful for scenarios like ABC classification.

[[DATESYTD]]

Context transition

Returns a set of dates in the year up to the last date visible in the filter context.

`DATESYTD ( <Dates> [, <YearEndDate>] )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[ALL]]

CALCULATE modifier

Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied.

`ALL ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

## Articles in the DAX 101 series

- *Computing running totals in DAX*
- [Counting working days in DAX](https://www.sqlbi.com/articles/counting-working-days-in-dax/)
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
