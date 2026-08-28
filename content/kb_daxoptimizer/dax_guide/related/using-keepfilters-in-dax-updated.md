---
title: "Using KEEPFILTERS in DAX"
url: "https://www.sqlbi.com/articles/using-keepfilters-in-dax-updated"
published: "2022-06-07"
updated: 
source: "sqlbi.com"
---

# Using KEEPFILTERS in DAX

> 发布：2022-06-07

[[KEEPFILTERS]] is a [[CALCULATE]] modifier used to change the way [[CALCULATE]] merges new filters with the outer filter context. Indeed, the default behavior of [[CALCULATE]] is to override existing filters. By using [[KEEPFILTERS]] you ask [[CALCULATE]] to add the new filter to the outer filter context, instead of overriding the outer filter.

**NOTE:** we suggest to also read [Best practices for using KEEPFILTERS in DAX](https://www.sqlbi.com/articles/best-practices-for-using-keepfilters-in-dax/).

## Single column filters

Let us start by looking at the effect of using [[KEEPFILTERS]] in a simple measure that uses [[CALCULATE]] with a single column filter. *AlwaysRed* shows the sales amount of red products, by forcing the *Product[Color]* to be read while the value of Sales Amount is being computed:

Measure in Sales table

```dax


AlwaysRed := 

CALCULATE (

    [Sales Amount],

    'Product'[Color] = "Red"

)

```

Bear in mind that the compact syntax of [[CALCULATE]] is translated into a longer syntax, because filters in [[CALCULATE]] are always tables. If you are not familiar with this concept, you may find more information by reading [Introducing CALCULATE in DAX](https://www.sqlbi.com/articles/introducing-calculate-in-dax/). The following is an equivalent formulation of the same measure:

Measure in Sales table

```dax


AlwaysRed := 

CALCULATE (

    [Sales Amount],

    FILTER ( 

        ALL ( 'Product'[Color] ), 

        'Product'[Color] = "Red" 

    )

)

```

The presence of [[ALL]] – which is explicit in this latter version while being implicit in the earlier version – means that the existence of an outer filter over *Product[Color]* is ignored by [[CALCULATE]]. This forces the color to be red regardless of any outer filter.

You can change this behavior by using [[KEEPFILTERS]] like in the *OnlyRed* measure. The *OnlyRed* measure is identical to *AlwaysRed*, except for the use of [[KEEPFILTERS]] around the filter:

Measure in Sales table

```dax


OnlyRed := 

CALCULATE (

    [Sales Amount],

    KEEPFILTERS ( 'Product'[Color] = "Red" )

)

```

You can see that the two measures behave differently regarding the outer filter. *AlwaysRed* always returns the sales volume of red products, as it replaces the outer filter with the red color. *OnlyRed* on the other hand returns a value only when red is already selected in the outer filter.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-34.png)

You can obtain this behavior by using different techniques, that you can find in many articles and blogs written prior to the introduction of [[KEEPFILTERS]] in DAX. By using [[VALUES]] as an additional filter in the compact syntax, or by replacing [[ALL]] with [[VALUES]] in the extended syntax, you achieve the same goal:

Measure in Sales table

```dax


OnlyRed Values := 

CALCULATE (

    [Sales Amount],

    Product[Color] = "Red",

    VALUES ( 'Product'[Color] )

)

```

Measure in Sales table

```dax


OnlyRed Values Ext := 

CALCULATE (

    [Sales Amount],

    FILTER ( 

        VALUES ( 'Product'[Color] ),

        Product[Color] = "Red"

    )

)

```

As you can see, the three measures behave the same way, even though the version using [[KEEPFILTERS]] is usually more efficient.  
![](https://cdn.sqlbi.com/wp-content/uploads/image2-31.png)

## Multiple column filters

[[KEEPFILTERS]] can also be used with conditions involving multiple columns. For example, if you want to test both the color and the brand in the same condition, you can author the following measure:

Measure in Sales table

```dax


AlwaysRedContoso := 

CALCULATE (

    [Sales Amount],

    OR ( 

        'Product'[Color] = "Red",

        'Product'[Brand] = "Contoso"

    )

)

```

When used in a matrix, it shows the same number on each row. Indeed, both the filter on the brand and the filter on the color were replaced.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-27.png)

Below, we see a scenario where [[KEEPFILTERS]] merges the filter on both columns with the outer filter context:

Measure in Sales table

```dax


OnlyRedContoso := 

CALCULATE (

    [Sales Amount],

    KEEPFILTERS ( 

        OR ( 

            'Product'[Color] = "Red",

            'Product'[Brand] = "Contoso"

        )

    )

)

```

This results in blanks everywhere except on the intersection of Contoso and Red.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-24.png)

As was the case with the single column scenario, you can obtain the same behavior by using multiple filters in [[CALCULATE]]. Alas, in the multiple column scenario you cannot leverage [[VALUES]], because [[VALUES]] works with a single column only. However, you can use [[SUMMARIZE]] to obtain the desired effect, both with the compact and with the extended syntax:

Measure in Sales table

```dax


OnlyRedContoso SUMMARIZE := 

CALCULATE (

    [Sales Amount],

    OR ( 

        'Product'[Color] = "Red",

        'Product'[Brand] = "Contoso"

    ),

    SUMMARIZE ( 'Product', 'Product'[Color], 'Product'[Brand] )

)

```

Measure in Sales table

```dax


OnlyRedContoso SUMMARIZE Ext := 

CALCULATE (

    [Sales Amount],

    FILTER ( 

        SUMMARIZE ( 'Product', 'Product'[Color], 'Product'[Brand] ),

        OR ( 

            'Product'[Color] = "Red",

            'Product'[Brand] = "Contoso"

        )

    )

)

```

## [[KEEPFILTERS]] in iterators

[[KEEPFILTERS]] can also be used with iterators. In this case, [[KEEPFILTERS]] is still a [[CALCULATE]] modifier although there seems to be no [[CALCULATE]]. We see this in the following example:

Measure in Sales table

```dax


Monthly Average :=

AVERAGEX (

    KEEPFILTERS ( VALUES ( 'Date'[Month] ) ),

    [Sales Amount]

)

```

Here, [[KEEPFILTERS]] is used to modify the semantics of the context transition induced by the *Sales Amount* measure reference. Although that aspect is too advanced for this introductory article, it is a nice transition to a more advanced piece. If you want to better understand when and how to use [[KEEPFILTERS]] with iterators, you can find more information here: [When to use KEEPFILTERS over iterators](https://www.sqlbi.com/articles/when-to-use-keepfilters-over-iterators/).

[[KEEPFILTERS]]

CALCULATE modifier

Changes the CALCULATE and CALCULATETABLE function filtering semantics.

`KEEPFILTERS ( <Expression> )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[ALL]]

CALCULATE modifier

Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied.

`ALL ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[VALUES]]

When a column name is given, returns a single-column table of unique values. When a table name is given, returns a table with the same columns and all the rows of the table (including duplicates) with the [additional blank row](https://www.sqlbi.com/articles/blank-row-in-dax/) caused by an invalid relationship if present.

`VALUES ( <TableNameOrColumnName> )`

[[SUMMARIZE]]

Creates a summary of the input table grouped by the specified columns.

`SUMMARIZE ( <Table> [, <GroupBy_ColumnName> [, [<Name>] [, [<Expression>] [, <GroupBy_ColumnName> [, [<Name>] [, [<Expression>] [, … ] ] ] ] ] ] ] )`

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
- [Using USERELATIONSHIP in DAX](https://www.sqlbi.com/articles/using-userelationship-in-dax/)
- [Mark as Date table](https://www.sqlbi.com/articles/mark-as-date-table/)
- [Row context in DAX](https://www.sqlbi.com/articles/row-context-in-dax/)
- [Filter context in DAX](https://www.sqlbi.com/articles/filter-context-in-dax/)
- [Introducing CALCULATE in DAX](https://www.sqlbi.com/articles/introducing-calculate-in-dax/)
- [Understanding context transition in DAX](https://www.sqlbi.com/articles/understanding-context-transition-in-dax/)
- *Using KEEPFILTERS in DAX*
- [Variables in DAX](https://www.sqlbi.com/articles/variables-in-dax/)
- [Using RELATED and RELATEDTABLE in DAX](https://www.sqlbi.com/articles/using-related-and-relatedtable-in-dax/)
- [Introducing ALLSELECTED in DAX](https://www.sqlbi.com/articles/introducing-allselected-in-dax/)
- [Introducing RANKX in DAX](https://www.sqlbi.com/articles/introducing-rankx-in-dax/)
