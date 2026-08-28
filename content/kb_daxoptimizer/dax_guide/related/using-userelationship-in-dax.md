---
title: "Using USERELATIONSHIP in DAX"
url: "https://www.sqlbi.com/articles/using-userelationship-in-dax"
published: "2020-07-03"
updated: 
source: "sqlbi.com"
---

# Using USERELATIONSHIP in DAX

> 发布：2020-07-03

If two tables are linked by more than one relationship, you can decide which relationship to activate by using [[USERELATIONSHIP]]. Indeed, you can only have one active relationship between any two tables. For example, you start with a model where *Sales* is related to *Date* through the *Date* column, like in the following picture.

![](https://cdn.sqlbi.com/wp-content/uploads/Using-USERELATIONSHIP-01.png)

If you create a second relationship, based on the *Delivery Date* columns, the second relationship is an inactive relationship.

![](https://cdn.sqlbi.com/wp-content/uploads/Using-USERELATIONSHIP-02.png)

Inactive relationships are – by nature – non-active. Consequently, if you filter by *Year* the report shows the sales whose *Order Date* belongs to the filtered year. *Delivery Date* is not used as part of the default filtering. In order to filter *Sales* based on *Delivery Date*, you can temporarily activate a relationship using [[USERELATIONSHIP]].

[[USERELATIONSHIP]] is a [[CALCULATE]] modifier, which instructs [[CALCULATE]] to temporarily activate the relationship. When [[CALCULATE]] has computed its result, the default relationship becomes active again.

The *Delivery Amount* measure below computes the amount delivered within a certain time period, in contrast with the ordered amount returned by the *Sales Amount* measure:

```dax


Delivery Amount :=

CALCULATE (

    [Sales Amount],

    USERELATIONSHIP ( Sales[Delivery Date], 'Date'[Date] )

)

```

You can compare *Delivery Amount* with *Sales Amount* in the following figure.

![](https://cdn.sqlbi.com/wp-content/uploads/Using-USERELATIONSHIP-03.png)

The arguments of USERELATIONSHIPS are the two columns that form the relationship. The order of the columns is not relevant, even though it is common practice to use the column of the many-side of the relationship (*Sales* in our example) first, and the one-side (*Date* in the example) second. With that said, it is just a standard convention; the opposite order does not alter the result.

The relationship must already be in the model. You cannot use [[USERELATIONSHIP]] to create a relationship on the fly. [[USERELATIONSHIP]] can only activate an existing relationship.

[[USERELATIONSHIP]]

CALCULATE modifier

Specifies an existing relationship to be used in the evaluation of a DAX expression. The relationship is defined by naming, as arguments, the two columns that serve as endpoints.

`USERELATIONSHIP ( <ColumnName1>, <ColumnName2> )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

## Articles in the DAX 101 series

- [Computing running totals in DAX](https://www.sqlbi.com/articles/computing-running-totals-in-dax/)
- [Counting working days in DAX](https://www.sqlbi.com/articles/counting-working-days-in-dax/)
- [Summing values for the total](https://www.sqlbi.com/articles/summing-values-for-the-total/)
- [Year-to-date filtering weekdays in DAX](https://www.sqlbi.com/articles/year-to-date-filtering-weekdays-in-dax/)
- [Creating a simple date table in DAX](https://www.sqlbi.com/articles/creating-a-simple-date-table-in-dax/)
- [Automatic time intelligence in Power BI](https://www.sqlbi.com/articles/automatic-time-intelligence-in-power-bi/)
- [Using CONCATENATEX in measures](https://www.sqlbi.com/articles/using-concatenatex-in-measures/)
- [Previous year up to a certain date](https://www.sqlbi.com/articles/previous-year-up-to-a-certain-date/)
- [Sorting months in fiscal calendars](https://www.sqlbi.com/articles/sorting-months-in-fiscal-calendars/)
- *Using USERELATIONSHIP in DAX*
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
