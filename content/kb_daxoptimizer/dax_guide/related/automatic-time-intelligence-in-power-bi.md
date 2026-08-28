---
title: "Automatic time intelligence in Power BI"
url: "https://www.sqlbi.com/articles/automatic-time-intelligence-in-power-bi"
published: "2020-08-17"
updated: 
source: "sqlbi.com"
---

# Automatic time intelligence in Power BI

> 发布：2020-08-17

Power BI offers the Auto Date/Time feature, which enables easy browsing of data by year, quarter, month and date. In the following model the Sales table contains the Order Date column, which contains only the date. The user can browse it using year and quarter by simply dropping the date into a matrix:  
![](https://cdn.sqlbi.com/wp-content/uploads/Auto-DateTime-01.png)

The Auto Date/Time feature can be turned on or off using the File|Options and Settings|Options dialog, under Data Load. It is turned on by default. You can find more details in this other video: [Disabling auto date-time in Power BI](https://youtu.be/xu3uDEHtCrg).

![](https://cdn.sqlbi.com/wp-content/uploads/Auto-DateTime-02.png)

In order to slice data by the different attributes of the date, Power BI automatically creates one date table for each date column in the model. Thus, if the model contains five date columns across one or more tables, there will be five automatic date tables in the model. Moreover, these tables are hidden from the user because Power BI handles them automatically.

The user can reference these tables by using a special syntax, which Power BI uses in the creation of quick calculations. For example, the quick calculation of year-to-date (YTD) based on the Sales[Order Date] column is the following:

```dax


Sales Amount YTD := 

IF (

    ISFILTERED ( 'Sales'[Order Date] ),

    ERROR ( "Time intelligence quick measures can only be grouped …" ),

	TOTALYTD ( 'Sales'[Sales Amount],  'Sales'[Order Date].[Date] )

)

```

The second parameter of [[TOTALYTD]] is not a simple column reference. It contains the table name, the column name and then, separated with a dot, another column name. The full meaning of this syntax is the following:

> Reference the Date column in the automatic date table created for the Order Date column in the Sales table.

Although this is a useful feature for very simple models, it comes with some severe limitations:

- It is not possible to customize the automatic date tables. By default, they contain Year, Quarter, Month and Date. If the user needs additional attributes, it is not possible to add them to the model.
- There is a separate automatic date table for each Date column in the model, making it very hard to build reports that slice multiple tables or that compute values based on different dates in a single table.

While the first point requires no further explanation, the second point is worth expanding on with a couple of examples. Consider a model containing two tables, Sales and Purchases:

![](https://cdn.sqlbi.com/wp-content/uploads/Auto-DateTime-03.png)

The automatic date table created for Sales[Order Date] filters only Sales and it will not filter Purchases. On the other hand, the automatic date table created for Purchases[Order Date] filters only Purchases. It is not possible to filter both tables using a single column. Thus, a report showing sales and purchases in the same visual shows up looking like this:

![](https://cdn.sqlbi.com/wp-content/uploads/Auto-DateTime-04.png)

Although very evident with multiple tables, the same problem is present when there are multiple kinds of dates in the same table. For example, Sales contains both the order date and the delivery date. The user might want to produce a report showing ordered amount and delivery amount in the same visual, like this:

![](https://cdn.sqlbi.com/wp-content/uploads/Auto-DateTime-05.png)

This is not an available option by relying solely on the automatic date table, because there will be two date tables: one for the sale date and one for the delivery date.

Thus, automatic date/time handling in Power BI only works in a scenario where there is a single table with a single Date column. Any other scenario requires a custom Date table and the authoring of time intelligence calculations in DAX, because it is not possible to use Quick Calculations. It is worth mentioning that the creation of a Date table is a very simple step, as described in the [Creating a Simple date table in DAX](https://www.sqlbi.com/articles/creating-a-simple-date-table-in-dax/) article.

[[TOTALYTD]]

Context transition

Evaluates the specified expression over the interval which begins on the first day of the year and ends with the last date in the specified date column (or in the evaluation context if a calendar reference is provided) after applying specified filters.

`TOTALYTD ( <Expression>, <Dates> [, <Filter>] [, <YearEndDate>] )`

## Articles in the DAX 101 series

- [Computing running totals in DAX](https://www.sqlbi.com/articles/computing-running-totals-in-dax/)
- [Counting working days in DAX](https://www.sqlbi.com/articles/counting-working-days-in-dax/)
- [Summing values for the total](https://www.sqlbi.com/articles/summing-values-for-the-total/)
- [Year-to-date filtering weekdays in DAX](https://www.sqlbi.com/articles/year-to-date-filtering-weekdays-in-dax/)
- [Creating a simple date table in DAX](https://www.sqlbi.com/articles/creating-a-simple-date-table-in-dax/)
- *Automatic time intelligence in Power BI*
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
