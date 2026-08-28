---
title: "From SQL to DAX: Filtering Data"
url: "https://www.sqlbi.com/articles/from-sql-to-dax-filtering-data"
published: "2020-08-17"
updated: 
source: "sqlbi.com"
---

# From SQL to DAX: Filtering Data

> 发布：2020-08-17

Consider the following SQL syntax:

```dax


SELECT * 

FROM DimProduct

WHERE Color = 'Red'

```

It corresponds to this DAX query using [[FILTER]]:

```dax


EVALUATE

FILTER ( Product, Product[Color] = "Red" )

```

You can also use a DAX query using [[CALCULATETABLE]]:

```dax


EVALUATE

CALCULATETABLE ( Product, Product[Color] = "Red" )

```

In case of a simple SQL query like the initial query, there are no semantic differences between the two corresponding DAX options. However, we see a different behavior when other expressions or a more complex filter condition are present.

Consider a double filter like this one:

```dax


SELECT * 

FROM DimProduct

WHERE Color = 'Red' AND ListPrice > 1000

```

You have two options using [[FILTER]]. The simplest one is applying the same logical expression to a single [[FILTER]]:

```dax


EVALUATE

FILTER (

    Product,

    AND ( Product[Color] = "Red", Product[ListPrice] > 1000 )

)

```

With this approach, the logical conditions (color red and list price greater than 1000) are applied to every row in Product, even if some optimization might happen internally to reduce the number of comparisons made.

This is the corresponding solution using [[CALCULATETABLE]]:

```dax


EVALUATE

CALCULATETABLE ( 

    Product,

    Product[Color] = "Red",

    Product[List Price] > 1000

)

```

The filter arguments in [[CALCULATETABLE]] are always put in a logical [[AND]] condition. Even if the results produced by [[FILTER]] and [[CALCULATETABLE]] are identical, the performance might be different. The [[CALCULATETABLE]] could be faster if the number of values returned by each logical condition on single columns is relatively small (more details in the [Understanding DAX Query Plans](https://www.sqlbi.com/articles/understanding-dax-query-plans/) white paper). In fact, every filter argument in [[CALCULATETABLE]] corresponds to an internal [[FILTER]] on a single column. The previous [[CALCULATETABLE]] syntax is internally rewritten as:

```dax


EVALUATE

CALCULATETABLE (

    Product,

    FILTER ( 

        ALL ( Product[Color] ), 

        Product[Color] = "Red" 

    ),

    FILTER (

        ALL ( Product[List Price] ),

        Product[List Price] > 1000

    )

)

```

Do not assume that [[CALCULATETABLE]] is good and [[FILTER]] is bad. Every [[CALCULATETABLE]] internally incorporates one or several [[FILTER]], and performance is generally determined by the granularity of each [[FILTER]] (even if the DAX engine might perform further optimization).

In the case of an [[OR]] condition, you should write the entire logical condition as a single condition even in [[CALCULATETABLE]], including a [[FILTER]] condition inside. Consider the following SQL statement:

```dax


SELECT * 

FROM DimProduct

WHERE Color = 'Red' OR Weight > 100

```

The corresponding DAX syntax using [[FILTER]] is:

```dax


EVALUATE

FILTER (

    Product,

    OR ( Product[Color] = "Red", Product[Weight] > 1000 )

)

```

The corresponding [[CALCULATETABLE]] version requires an explicit [[FILTER]] argument in order to specify a logical condition that includes two columns of the same table (in this case, no automatic [[FILTER]] rewriting is possible).

```dax


EVALUATE

CALCULATETABLE (

    Product,

    FILTER (

        Product,

        OR ( Product[Color] = "Red", Product[Weight] > 1000 )

    )

)

```

At this point, you might think that [[FILTER]] is simpler to use. However, you should always consider the [[CALCULATETABLE]] option, because of the different behavior when you have nested calculations.

For example, consider the following SQL query that returns red products with a total sales amount greater than 100,000 and an average sales amount greater than 3,000 in the calendar year 2006.

```dax


SELECT *

FROM DimProduct p

WHERE

    Color = 'Red'

    AND ( SELECT SUM([SalesAmount])

          FROM [FactInternetSales] s

          INNER JOIN DimDate d

            ON s.OrderDateKey = d.DateKey

          WHERE s.ProductKey = p.ProductKey

            AND d.CalendarYear = 2006

        ) > 100000

    AND ( SELECT AVG([SalesAmount])

          FROM [FactInternetSales] s

          INNER JOIN DimDate d

            ON s.OrderDateKey = d.DateKey

          WHERE s.ProductKey = p.ProductKey

            AND d.CalendarYear = 2006

        ) > 3000

```

The equivalent DAX expression using only [[FILTER]] is shorter than the SQL expression. However, you would still specify the Calendar Year filter in two calculations (sum and average):

```dax


EVALUATE

FILTER (

    FILTER ( Product, Product[Color] = "Red" ),

    AND (

        CALCULATE (

            SUM ( 'Internet Sales'[Sales Amount] ),

            'Date'[Calendar Year] = 2006

        ) > 100000,

        CALCULATE (

            AVERAGE ( 'Internet Sales'[Sales Amount] ),

            'Date'[Calendar Year] = 2006

        ) > 3000

    )

)



```

Using [[CALCULATETABLE]], the filter arguments (color and calendar year) are applied to the entire expression specified in the first argument. For this reason, the two [[CALCULATE]] expressions in the [[FILTER]] of the following DAX query do not have to include the filter on calendar year, because it is “inherited” from the outer [[CALCULATETABLE]] filters.

```dax


EVALUATE

CALCULATETABLE (

    FILTER (

        Product,

        AND ( 

            CALCULATE ( SUM ( 'Internet Sales'[Sales Amount] ) ) > 100000,

            CALCULATE ( AVERAGE ( 'Internet Sales'[Sales Amount] ) ) > 3000

        )

    ),

    Product[Color] = "Red",

    'Date'[Calendar Year] = 2006

)

```

Using [[CALCULATETABLE]] you propagate a filter to all the expressions embedded in the first argument. This results in shorter syntax simplifying the query, even if it requires particular attention – outer [[CALCULATETABLE]] and [[CALCULATE]] statements manipulate the filter context and affect any inner expression.

[[FILTER]]

Returns a table that has been filtered.

`FILTER ( <Table>, <FilterExpression> )`

[[CALCULATETABLE]]

Context transition

Evaluates a table expression in a context modified by filters.

`CALCULATETABLE ( <Table> [, <Filter> [, <Filter> [, … ] ] ] )`

[[AND]]

Checks whether all arguments are TRUE, and returns TRUE if all arguments are TRUE.

`AND ( <Logical1>, <Logical2> )`

[[OR]]

Returns TRUE if any of the arguments are TRUE, and returns FALSE if all arguments are FALSE.

`OR ( <Logical1>, <Logical2> )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

## Articles in the From SQL to DAX series

- [From SQL to DAX: String Comparison](https://www.sqlbi.com/articles/from-sql-to-dax-string-comparison/)
- *From SQL to DAX: Filtering Data*
- [From SQL to DAX: Grouping Data](https://www.sqlbi.com/articles/from-sql-to-dax-grouping-data/)
- [From SQL to DAX: IN and EXISTS](https://www.sqlbi.com/articles/from-sql-to-dax-in-and-exists/)
- [From SQL to DAX: Joining Tables](https://www.sqlbi.com/articles/from-sql-to-dax-joining-tables/)
- [From SQL to DAX: Implementing NULLIF and COALESCE in DAX](https://www.sqlbi.com/articles/from-sql-to-dax-implementing-nullif-and-coalesce-in-dax/)
- [From SQL to DAX: Projection](https://www.sqlbi.com/articles/from-sql-to-dax-projection/)
