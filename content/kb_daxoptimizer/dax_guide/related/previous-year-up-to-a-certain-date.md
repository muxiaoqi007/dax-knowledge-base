---
title: "Previous year up to a certain date"
url: "https://www.sqlbi.com/articles/previous-year-up-to-a-certain-date"
published: "2020-12-04"
updated: 
source: "sqlbi.com"
---

# Previous year up to a certain date

> 发布：2020-12-04

If it is necessary to compare one month against the same month in the previous year, this calculation provides a good starting point:

```dax


Previous Year = 

CALCULATE ( 

    [Sales Amount], 

    SAMEPERIODLASTYEAR ( 'Date'[Date] ) 

)

```

With this measure, a report can show current year sales against previous year sales:

![](https://cdn.sqlbi.com/wp-content/uploads/Previous-year-up-to-a-certain-date-01.png)

Unfortunately, the calculation is not perfect. At the year level, it compares the full previous year against an incomplete current year – in this example there are no sales after September 5th in the current year.

Besides, the problem appears not only at the year level, but also at the month level. Indeed, in September the *Previous Year* measure returns sales for the entire month of September in the previous year. The comparison is unfair, as there are only five days’ worth of sales in September of the current year.

A possible solution is to create a calculated column in the Date table in order to remove dates in the past that should be ignored. If the last date in the fact table is September 5th for the current year, then all the dates after September 5th in previous years can be marked to avoid considering them in the calculation.

The code for that calculated column is:

```dax


IsPast = 

VAR LastSaleDate = MAX ( Sales[Order Date] )

VAR LastSaleDatePY = EDATE ( LastSaleDate, -12 )

RETURN

    'Date'[Date] <= LastSaleDatePY

```

This code stores the last date of sales into *LastSaleDate*, then it moves it back one year (twelve months) using the [[EDATE]] function. Finally, it checks whether the current date is earlier than the last date in the previous year. The next figure shows that *IsPast* calculated column, which changes value on September 6th, 2008 (in the demo model, the last year is 2009):

![](https://cdn.sqlbi.com/wp-content/uploads/Previous-year-up-to-a-certain-date-02.png)

Once the column is in place, the *Adjusted Previous Year* measure can compute a variation of the *Previous Year* calculation. The variation specifies that it only considers dates in the previous year for which the *IsPast* column is True:

```dax


Adjusted Previous Year = 

CALCULATE(

    [Sales Amount], 

    SAMEPERIODLASTYEAR ( 'Date'[Date] ), 

    'Date'[IsPast] = TRUE 

)

```

The next figure shows the result – it provides a fairer comparison between current and previous years:

![](https://cdn.sqlbi.com/wp-content/uploads/Previous-year-up-to-a-certain-date-03.png)

[[EDATE]]

Returns the date that is the indicated number of months before or after the start date.

`EDATE ( <StartDate>, <Months> )`

## Articles in the DAX 101 series

- [Computing running totals in DAX](https://www.sqlbi.com/articles/computing-running-totals-in-dax/)
- [Counting working days in DAX](https://www.sqlbi.com/articles/counting-working-days-in-dax/)
- [Summing values for the total](https://www.sqlbi.com/articles/summing-values-for-the-total/)
- [Year-to-date filtering weekdays in DAX](https://www.sqlbi.com/articles/year-to-date-filtering-weekdays-in-dax/)
- [Creating a simple date table in DAX](https://www.sqlbi.com/articles/creating-a-simple-date-table-in-dax/)
- [Automatic time intelligence in Power BI](https://www.sqlbi.com/articles/automatic-time-intelligence-in-power-bi/)
- [Using CONCATENATEX in measures](https://www.sqlbi.com/articles/using-concatenatex-in-measures/)
- *Previous year up to a certain date*
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
