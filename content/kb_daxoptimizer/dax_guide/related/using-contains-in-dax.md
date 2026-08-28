---
title: "Using CONTAINS in DAX"
url: "https://www.sqlbi.com/articles/using-contains-in-dax"
published: "2021-08-17"
updated: 
source: "sqlbi.com"
---

# Using CONTAINS in DAX

> 发布：2021-08-17

The [[CONTAINS]] function in DAX has been available since the very first version of the language in 2010. In the evolution of the language, new syntaxes and functions have been added, and several use cases for [[CONTAINS]] that were valid many years ago are no longer considered good practice. The goal of this article is to clarify when [[CONTAINS]] is a good practice and when there are better alternatives to solve common problems.

The [[CONTAINS]] function returns TRUE if a specified value is found in at least one row in the table. For example, the following query checks whether there is at least one row in the *Product* table where the *Color* is Red and the *Brand* is Contoso:

```dax


EVALUATE

{

    CONTAINS (

        'Product',

        'Product'[Color], "Red",

        'Product'[Brand], "Contoso"

    )

}

```

The same result could have been obtained with the following expression based on [[ISEMPTY]] and [[FILTER]], but the [[CONTAINS]] version is shorter and might be faster in more complex scenarios. In this simple example, the query plan is identical, and the only difference is the readability of the code:

```dax


EVALUATE

{

    NOT ISEMPTY (

        FILTER (

            'Product',

            'Product'[Color] = "Red"

                && 'Product'[Brand] = "Contoso"

        )

    )

}

```

Like we said, the [[CONTAINS]] function can be a good choice when you want to check whether at least one row in a table meets certain conditions in a subset of the columns of the entire table. We have more modern alternatives using IN and [[TREATAS]], but the resulting code for the use case shown is probably harder to read and maintain.

For example, we can write the same condition using IN. That said we need [[SELECTCOLUMNS]] to reference only two columns of the table, *Color* and *Brand*. However, the query plan is still identical to the previous examples:

```dax


EVALUATE

{

    ( "Red", "Contoso" )

        IN SELECTCOLUMNS (

            'Product',

            "Color", 'Product'[Color],

            "Brand", 'Product'[Brand]

        )

}

```

Using [[TREATAS]] makes the code much harder to read, and in this particular case the query plan is also more complex. However, we see later in the article that [[TREATAS]] can be a better choice when you implement a virtual relationship pattern:

```dax


EVALUATE

{

    NOT ISEMPTY (

        CALCULATETABLE (

            'Product',

            TREATAS (

                {

                    ( "Red", "Contoso" )

                },

                'Product'[Color],

                'Product'[Brand]

            )

        )

    )

}

```

## Better alternatives to [[CONTAINS]]

When you do not have a relationship between two tables, you can propagate a filter by using a specific DAX pattern for [virtual relationships](https://www.sqlbi.com/articles/physical-and-virtual-relationships-in-dax/). In 2012, using [[CONTAINS]] was the best practice to implement said technique, but in 2021 it is likely the worst choice among the alternatives we have now.

Here is an example. We disabled the relationship between *Sales* and *Product* in the following snippet by using [[CROSSFILTER]]. The [[CONTAINS]] pattern in the *Sales Virtual Relationship [[CONTAINS]]* measure produces the effect of the missing relationship over the *ProductKey* column, though with the worst performance:

```dax


DEFINE

    MEASURE Sales[Sales Virtual Relationship CONTAINS] =

        CALCULATE (

            [Sales Amount],

            FILTER (

                ALL ( Sales[ProductKey] ),

                CONTAINS (

                    VALUES ( 'Product'[ProductKey] ),

                    'Product'[ProductKey], Sales[ProductKey]

                )

            )

        )

EVALUATE

CALCULATETABLE (

    SUMMARIZECOLUMNS (

        'Product'[Category],

        "Sales Amount", [Sales Amount],

        "Sales Amount CONTAINS", [Sales Virtual Relationship CONTAINS]

    ),

    CROSSFILTER ( Sales[ProductKey], 'Product'[ProductKey], NONE )

)

```

| Category | Sales Amount | Sales Amount CONTAINS |
| --- | --- | --- |
| Audio | 30,591,343.98 | 384,518.16 |
| TV and Video | 30,591,343.98 | 4,392,768.29 |
| Computers | 30,591,343.98 | 6,741,548.73 |
| Cameras and camcorders | 30,591,343.98 | 7,192,581.95 |
| Cell phones | 30,591,343.98 | 1,604,610.26 |
| Music, Movies and Audio Books | 30,591,343.98 | 314,206.74 |
| Games and Toys | 30,591,343.98 | 360,652.81 |
| Home Appliances | 30,591,343.98 | 9,600,457.04 |

In this case the best practice is to remove the [[FILTER]] iterator and use [[TREATAS]] to change the data lineage of the list of products retrieved from the filter context. We see this in the following *Sales Virtual Relationship [[TREATAS]]* measure:

```dax


DEFINE

    MEASURE Sales[Sales Virtual Relationship TREATAS] =

        CALCULATE (

            [Sales Amount],

            TREATAS (

                VALUES ( 'Product'[ProductKey] ),

                Sales[ProductKey]

            )

        )

EVALUATE

CALCULATETABLE (

    SUMMARIZECOLUMNS (

        'Product'[Category],

        "Sales Amount", [Sales Amount],

        "Sales Amount TREATAS", [Sales Virtual Relationship TREATAS]

    ),

    CROSSFILTER ( Sales[ProductKey], 'Product'[ProductKey], NONE )

)

```

| Category | Sales Amount | Sales Amount TREATAS |
| --- | --- | --- |
| Audio | 30,591,343.98 | 384,518.16 |
| TV and Video | 30,591,343.98 | 4,392,768.29 |
| Computers | 30,591,343.98 | 6,741,548.73 |
| Cameras and camcorders | 30,591,343.98 | 7,192,581.95 |
| Cell phones | 30,591,343.98 | 1,604,610.26 |
| Music, Movies and Audio Books | 30,591,343.98 | 314,206.74 |
| Games and Toys | 30,591,343.98 | 360,652.81 |
| Home Appliances | 30,591,343.98 | 9,600,457.04 |

[[TREATAS]] is a function introduced in 2017 that is not available in Analysis Services 2016 and is not supported in Power Pivot for Excel. If you write code for these products, you can use an equivalent pattern based on [[INTERSECT]] that is not as good as the one with [[TREATAS]], but is still better than the one based on [[CONTAINS]]:

```dax


DEFINE

    MEASURE Sales[Sales Virtual Relationship INTERSECT] =

        CALCULATE (

            [Sales Amount],

            INTERSECT (

                ALL ( Sales[ProductKey] ),

                VALUES ( 'Product'[ProductKey] )

            )

        )

EVALUATE

CALCULATETABLE (

    SUMMARIZECOLUMNS (

        'Product'[Category],

        "Sales Amount", [Sales Amount],

        "Sales Amount INTERSECT", [Sales Virtual Relationship INTERSECT]

    ),

    CROSSFILTER ( Sales[ProductKey], 'Product'[ProductKey], NONE )

)

```

| Category | Sales Amount | Sales Amount INTERSECT |
| --- | --- | --- |
| Audio | 30,591,343.98 | 384,518.16 |
| TV and Video | 30,591,343.98 | 4,392,768.29 |
| Computers | 30,591,343.98 | 6,741,548.73 |
| Cameras and camcorders | 30,591,343.98 | 7,192,581.95 |
| Cell phones | 30,591,343.98 | 1,604,610.26 |
| Music, Movies and Audio Books | 30,591,343.98 | 314,206.74 |
| Games and Toys | 30,591,343.98 | 360,652.81 |
| Home Appliances | 30,591,343.98 | 9,600,457.04 |

## Better alternatives to NOT [[CONTAINS]]

The NOT [[CONTAINS]] condition can retrieve rows that are not matching a join condition over multiple columns. For example, the following query returns the list of stores in cities with no customers; our sample database has similar cases, we understand that in the real world this condition should be extremely unusual:

```dax


EVALUATE

FILTER (

    Store,

    NOT CONTAINS (

        Customer,

        Customer[Continent], Store[Continent],

        Customer[State], Store[State],

        Customer[City], Store[City]

    )

)

```

We can write the same syntax using a NOT … IN condition, with no differences in the query plan:

```dax


EVALUATE

FILTER (

    Store,

    NOT ( Store[Continent], Store[State], Store[City] )

        IN SELECTCOLUMNS (

            Customer,

            "Continent", Customer[Continent],

            "State", Customer[State],

            "City", Customer[City]

        )

)

```

However, the previous two techniques are not very efficient in DAX, because it is always better to manipulate filters as tables. Because we want to obtain a list of stores placed in cities with no customers, it is more efficient to create a filter with the list of cities where there are stores and no customers, and use that filter to get the list of stores. The following code uses [[EXCEPT]] to remove the list of the cities with customers from the list of the cities with stores. The remaining cities are used to filter the stores:

```dax


EVALUATE 

CALCULATETABLE (

    Store,

    EXCEPT (

        SUMMARIZE ( Store, Store[Continent], Store[State], Store[City] ),

        SUMMARIZE ( Customer, Customer[Continent], Customer[State], Customer[City] )

    )

)

```

This last example shows that we do not have a specific syntax faster than [[CONTAINS]]. We have to transform the filter to obtain the required result by reducing the iterations whenever possible. Because [[CONTAINS]] is often used in an iterator, our goal is to remove the iterator rather than focus on an alternative to the [[CONTAINS]] function in the same predicate.

## Conclusions

The [[CONTAINS]] function is often used in many examples created with the first version of the DAX language. Many use cases where [[CONTAINS]] was the only option are now better solved with different approaches, in particular when you can replace an iterator with a table function that can be better optimized by the DAX engine.

[[CONTAINS]]

Returns TRUE if there exists at least one row where all columns have specified values.

`CONTAINS ( <Table>, <ColumnName>, <Value> [, <ColumnName>, <Value> [, … ] ] )`

[[ISEMPTY]]

Returns true if the specified table or table-expression is Empty.

`ISEMPTY ( <Table> )`

[[FILTER]]

Returns a table that has been filtered.

`FILTER ( <Table>, <FilterExpression> )`

[[TREATAS]]

Treats the columns of the input table as columns from other tables. For each column, filters out any values that are not present in its respective output column.

`TREATAS ( <Expression>, <ColumnName> [, <ColumnName> [, … ] ] )`

[[SELECTCOLUMNS]]

Returns a table with selected columns from the table and new columns specified by the DAX expressions.

`SELECTCOLUMNS ( <Table> [[, <Name>], <Expression> [[, <Name>], <Expression> [, … ] ] ] )`

[[CROSSFILTER]]

CALCULATE modifier

Specifies cross filtering direction to be used in the evaluation of a DAX expression. The relationship is defined by naming, as arguments, the two columns that serve as endpoints.

`CROSSFILTER ( <LeftColumnName>, <RightColumnName>, <CrossFilterType> )`

[[INTERSECT]]

Returns the rows of left-side table which appear in right-side table.

`INTERSECT ( <LeftTable>, <RightTable> )`

[[EXCEPT]]

Returns the rows of left-side table which do not appear in right-side table.

`EXCEPT ( <LeftTable>, <RightTable> )`
