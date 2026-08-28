---
title: "Differences between ALL and ALLCROSSFILTERED"
url: "https://www.sqlbi.com/articles/differences-between-all-and-allcrossfiltered"
published: "2025-01-27"
updated: 
source: "sqlbi.com"
---

# Differences between ALL and ALLCROSSFILTERED

> 发布：2025-01-27

Have you ever wondered what the subtle difference between [[ALL]] and [[ALLCROSSFILTERED]] might be? The family of [[ALL]] functions and modifiers includes some common functions, like [[ALL]] and [[ALLSELECTED]], and some fancier and less frequently-used functions, like [[ALLNOBLANKROW]] and [[ALLCROSSFILTERED]]. This article discusses what [[ALLCROSSFILTERED]] is, why it is there in DAX, and when and how developers should use it.

Let us start with the name: [[ALLCROSSFILTERED]] should really be named ALLCROSSFILTERING. Because this is what it does. It does not remove the filter from the columns filtered by a table. Instead, it removes the filter from the columns that are filtering the table. However, it is what it is: it is named [[ALLCROSSFILTERED]]; make peace with this.

[[ALLCROSSFILTERED]] can only be used as a [[CALCULATE]] modifier and not as a table function. [[ALLCROSSFILTERED]] has only one argument, which must be a table. [[ALLCROSSFILTERED]] removes all the filters on an expanded table (similarly to what [[ALL]] and [[REMOVEFILTERS]] do). [[ALLCROSSFILTERED]] also removes all the filters **on columns and tables that are cross-filtering the table because of limited relationships, or because of bidirectional cross-filters set on relationships directly or indirectly connected to the expanded table**.

Despite being correct, the description we just provided requires further explanation. When used as a [[CALCULATE]] modifier with a table as the argument, [[ALL]] (like [[REMOVEFILTERS]]) removes filters from the expanded table of its argument.

When you use *[[ALL]] (Sales)* or *[[REMOVEFILTERS]] ( Sales )*, the engine removes filters from *Sales* and any table linked to *Sales* with a regular relationship; this includes *Product*, *Customer*, *Store*, and so on. Tables like *Product* belong to the expanded *Sales* table. Hence, *[[ALL]] (Sales)* removes filters from *Product*, too. However, table expansion uses only regular relationships. Table expansion does not occur when tables are linked through a limited relationship.

If you are not familiar with the concept of expanded tables, we recommend you peruse this article: <https://www.sqlbi.com/articles/expanded-tables-in-dax/>: it introduces this important theoretical concept in DAX, and you should know this like the back of your hand if you are serious about DAX.

To demonstrate the behavior, we modified the relationship between *Product* and *Sales* by using a many-to-many cardinality relationship, so we have a limited relationship. The following figure shows the two tables, *Product* and *Sales*, linked through a limited relationship.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-99.png)

Because the relationship is limited, *Sales* does not expand to *Product*. Hence, when we use *[[ALL]] (Sales)*, we remove all filters from *Sales* but not *Product*. If developers need to make sure any filter on *Sales* is removed, including filters from tables related through limited relationships or bidirectional filters, then [[ALLCROSSFILTERED]] is the function to use. To demonstrate the behavior, we created three measures:

Measures in the Sales table

Measure in Sales table

```dax


# Sales = COUNTROWS ( Sales )

```

Measure in Sales table

```dax


# All Sales = 

CALCULATE ( 

    [# Sales], 

    ALL ( Sales ) 

)

```

Measure in Sales table

```dax


# All Crossfiltered Sales = 

CALCULATE(

    [# Sales],

    ALLCROSSFILTERED ( Sales )

)

```

When used in a matrix that slices by *Product[Brand]*, we can see the different results.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-96.png)

*# All Sales* shows the same value as *# Sales*. Indeed, [[ALL]] removes filters from the *Sales* table and any table linked through a regular relationship. However, in the scenario we are showing, *Product* and *Sales* are not linked through a regular relationship; they are linked through a limited relationship. Consequently, *Sales* does not expand to *Product* and *[[ALL]] ( Sales )* has no effect on *Product*.

In this scenario, [[ALLCROSSFILTERED]] removes filters from any table that cross-filters *Sales*, including *Product*; consequently, the result is – as expected – the total number of products.

Table expansion does not happen through limited relationships. Limited relationships may appear in your semantic model because of multiple causes:

- **Many-to-many cardinality relationships**: these relationships are limited by nature, even though they link tables in the same data island.
- **Relationships between tables in different data islands**: whenever two tables reside in different data islands, the relationship linking them is limited.
- **Composite models**: this is a subset of the previous category. A table in the local model linked to a table in the remote model is a relationship between tables in different islands, therefore it is limited.

In any of these scenarios, using [[ALLCROSSFILTERED]] is a safer way to remove filters from a table, compared to the more commons [[ALL]] and [[REMOVEFILTERS]].

Let us see a more practical example. We want to create a composite model with a new table (*Interesting Brands*) containing the brand, its total sales, and a flag that identifies whether the total sales for that brand is above or below the average over all the brands. Depending on whether the brand is above or below, we allocate a percentage of its sales volume for marketing, so we spend more money on the best-performing brands:

Calculated table

```dax


Interesting Brands =

VAR BrandsAndSales =

    ADDCOLUMNS (

        ALLNOBLANKROW ( 'Product'[Brand] ),

        "Brand Sales", [Sales Amount]

    )

VAR AverageSales =

    AVERAGEX (

        BrandsAndSales,

        [Brand Sales]

    )

VAR CategorizedBrands =

    ADDCOLUMNS (

        BrandsAndSales,

        "Brand Class",

            IF (

                [Brand Sales] >= AverageSales,

                "Above Average",

                "Below Average"

            )

    )

VAR Result =

    ADDCOLUMNS (

        CategorizedBrands,

        "Investment Pct",

            IF (

                [Brand Class] = "Above Average",

                0.03,

                0.01

            )

    )

RETURN

    Result

```

The *Interesting Brands* table contains four columns.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-88.png)

We then create a relationship between *Product* and *Interesting Brand*. Because *Brand* is a key in *Interesting Brands*, it is a one-to-many relationship. However, being a relationship between different data islands, it is limited.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-82.png)

Based on this model, for each brand we want to show the sales amount, the marketing investment, and the percentage of investment against the total. The desired report looks like the following.

![](https://cdn.sqlbi.com/wp-content/uploads/image5-73.png)

The *Marketing Investment* measure is straightforward, as it involves a simple [[SUMX]]:

Measure in Sales table

```dax


Marketing Investment = 

SUMX ( 

    'Interesting Brands',

    [Sales Amount] * 'Interesting Brands'[Investment Pct]

)

```

The *% of Mktg Budget* looks simple, but it hides a trap. The first attempt is wrong:

Measure in Sales table

```dax


% of Mktg Budget = 

DIVIDE (

    [Marketing Investment], 

    CALCULATE( [Marketing Investment], ALL ( Sales ) ) 

)

```

Please note that we used *[[ALL]] ( Sales )* to remove filters from the entire *Sales* table. Surely, we could have used [[ALL]] on *Interesting Brands*, or also [[ALL]] with no arguments. However, this is an educational article, and mistakes are somewhat welcome to support learning. Besides, there is nothing wrong with using *[[ALL]] ( Sales )* to remove filters from the expanded table *Sales*. Except for the fact that – in this special case – it is not going to work because *[[ALL]] ( Sales )* removes the filter from the expanded *Sales* table, which does not include *Interesting Brands*. Remember: the relationship between *Sales* and *Interesting Brands* is limited; that is, no expansion is happening. Indeed, the result is always 100%, which is wrong.

![](https://cdn.sqlbi.com/wp-content/uploads/image6-66.png)

The filter from *Interesting Brands* is not being removed at the denominator, resulting in a constant value of 100%. In this scenario, because there is a limited relationship involved, using [[ALLCROSSFILTERED]] is the right thing to do:

Measure in Sales table

```dax


% of Mktg Budget = 

DIVIDE (

    [Marketing Investment], 

    CALCULATE( [Marketing Investment], ALLCROSSFILTERED ( Sales ) ) 

)

```

When using [[ALLCROSSFILTERED]], the filter in *Interesting Brands* is removed, too, resulting in the correct calculation.

There are no drawbacks in using [[ALLCROSSFILTERED]] rather than [[ALL]], neither from the performance point of view, nor from the semantic point of view.

In conclusion, an interesting question would be: why don’t we all use [[ALLCROSSFILTERED]] instead of [[ALL]] or [[REMOVEFILTERS]]? Basically: habits. We got used over the years to using [[ALL]], we learned later to use [[REMOVEFILTERS]] (which is nothing but an alias for [[ALL]])… It will take time for people to get used to using [[ALLCROSSFILTERED]] rather than [[ALL]].

Besides, in most scenarios (no limited relationships in the model), [[ALL]] works just fine. Therefore, do not rush to change all the references to [[ALL]] with [[ALLCROSSFILTERED]] in your code. However, when dealing with models where you want to remove all filters from the cross-filtering tables, then [[ALLCROSSFILTERED]] is a perfect option.

[[ALL]]

CALCULATE modifier

Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied.

`ALL ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[ALLCROSSFILTERED]]

CALCULATE modifier

Clear all filters which are applied to the specified table.

`ALLCROSSFILTERED ( <TableName> )`

[[ALLSELECTED]]

CALCULATE modifier

Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied inside the query, but keeping filters that come from outside.

`ALLSELECTED ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[ALLNOBLANKROW]]

CALCULATE modifier

Returns all the rows except blank row in a table, or all the values in a column, ignoring any filters that might have been applied.

`ALLNOBLANKROW ( <TableNameOrColumnName> [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[REMOVEFILTERS]]

CALCULATE modifier

Clear filters from the specified tables or columns.

`REMOVEFILTERS ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[SUMX]]

Returns the sum of an expression evaluated for each row in a table.

`SUMX ( <Table>, <Expression> )`
