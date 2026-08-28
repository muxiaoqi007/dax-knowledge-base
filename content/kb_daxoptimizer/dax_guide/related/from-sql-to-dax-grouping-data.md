---
title: "From SQL to DAX: Grouping Data"
url: "https://www.sqlbi.com/articles/from-sql-to-dax-grouping-data"
published: "2020-08-17"
updated: 
source: "sqlbi.com"
---

# From SQL to DAX: Grouping Data

> 发布：2020-08-17

Consider the following SQL query:

```dax


SELECT

    OrderDate,

    SUM(SalesAmount) AS Sales

FROM   

    FactInternetSales

GROUP BY 

    OrderDate

```

It corresponds to this DAX query using [[SUMMARIZE]]:

```dax


EVALUATE

SUMMARIZE ( 

    'Internet Sales',

    'Internet Sales'[Order Date],

    "Sales", SUM ( 'Internet Sales'[Sales Amount] )

)

```

You can also use a syntax that produces the same result, even if it is not semantically the same, as you will see later:

```dax


EVALUATE

ADDCOLUMNS ( 

    VALUES ( 'Internet Sales'[Order Date] ),

    "Sales", CALCULATE ( SUM ( 'Internet Sales'[Sales Amount] ) )

)

```

Using [[ADDCOLUMNS]] you need to apply [[CALCULATE]] because you have to transform the row context (defined by the iteration in the Order Date colum) into a filter context. This is not required in [[SUMMARIZE]], because the expression specified is already executed in a filter context of the group you specified.

The semantic difference between [[ADDCOLUMNS]] and [[SUMMARIZE]] becomes clearer as soon as we involve more tables in the grouping operation. For example, consider the following SQL query:

```dax


SELECT

    d.CalendarYear,

    SUM(s.SalesAmount) AS Sales

FROM

    FactInternetSales s

LEFT JOIN DimDate d

    ON s.OrderDateKey = d.DateKey

GROUP BY

    d.CalendarYear

```

In this case, if the data model has a relationship between DimDate and Internet Sales, the DAX expression implicitly use it. This is the corresponding DAX syntax (the order by only guarantees that data is displayed as in the following table):

```dax


EVALUATE

SUMMARIZE ( 

    'Internet Sales',

    'Date'[Calendar Year],

    "Sales", SUM ( 'Internet Sales'[Sales Amount] )

)

ORDER BY 'Date'[Calendar Year]

```

You obtain this result in Adventure Works Tabular Model SQL 2012:

|  |  |
| --- | --- |
| **Date[Calendar Year]** | **[Sales]** |
| 2005 | 3266373,6566 |
| 2006 | 6530343,5264 |
| 2007 | 9791060,2977 |
| 2008 | 9770899,74 |

The table passed as first argument is joined with tables required to reach the column(s) used to group data. Thus, [[SUMMARIZE]] performs the equivalent SQL operations [[DISTINCT]] and GROUP BY, and it includes a [[LEFT]] JOIN between a table and one or more lookup tables.

You can avoid the [[SUMMARIZE]] by using this other DAX syntax:

```dax


EVALUATE

ADDCOLUMNS ( 

    VALUES ( 'Date'[Calendar Year] ),

    "Sales", CALCULATE ( SUM ( 'Internet Sales'[Sales Amount] ) )

)

ORDER BY 'Date'[Calendar Year]

```

However, this returns a different result, including calendar years defined in Date table that do not have any corresponding data in the Internet Sales table.

|  |  |
| --- | --- |
| **Date[Calendar Year]** | **[Sales]** |
| 2005 | 3266373,6566 |
| 2006 | 6530343,5264 |
| 2007 | 9791060,2977 |
| 2008 | 9770899,74 |
| 2009 |  |
| 2010 |  |

This happens because the last DAX query corresponds to the following SQL syntax:

```dax


SELECT

    d.CalendarYear,

    SUM(s.SalesAmount) AS Sales

FROM

    DimDate d

LEFT JOIN FactInternetSales s

    ON d.DateKey = s.OrderDateKey

GROUP BY

    d.CalendarYear

```

As you see, [[SUMMARIZE]] is not required to perform a JOIN, but different DAX syntaxes executes different join types. In this case, the tables used in the [[LEFT]] JOIN are inverted.

*Note: you can find more information about differences between ADDCOLUMN and [[SUMMARIZE]] in the article [Best Practices Using SUMMARIZE and ADDCOLUMNS](https://www.sqlbi.com/articles/best-practices-using-summarize-and-addcolumns/).*

Finally, consider the HAVING condition in the following SQL query:

```dax


SELECT

    d.CalendarYear,

    SUM(s.SalesAmount) AS Sales

FROM

    DimDate d

LEFT JOIN FactInternetSales s

    ON d.DateKey = s.OrderDateKey

GROUP BY

    d.CalendarYear

HAVING

    SUM(s.SalesAmount) > 8000000

ORDER BY

    d.CalendarYear

```

This query returns only years with sales greater than 8 million:

|  |  |
| --- | --- |
| **Date[Calendar Year]** | **[Sales]** |
| 2007 | 9791060,2977 |
| 2008 | 9770899,74 |

DAX does not have a syntax corresponding to the HAVING condition. After all, you might obtain the same result in SQL by applying a WHERE condition to a subquery, like in the following example.

```dax


SELECT

    CalendarYear,

    Sales

FROM

    ( SELECT

        d.CalendarYear,

        SUM(s.SalesAmount) AS Sales

      FROM

        DimDate d

      LEFT JOIN FactInternetSales s

        ON d.DateKey = s.OrderDateKey

      GROUP BY

        d.CalendarYear

    ) YearlySales

WHERE

    Sales > 8000000

ORDER BY

    CalendarYear

```

In DAX you need the same approach to write a syntax corresponding to the HAVING condition: just [[FILTER]] the result of a [[SUMMARIZE]] or [[ADDCOLUMNS]] function call, as you see in the following examples.

```dax


EVALUATE

FILTER (

    ADDCOLUMNS ( 

        VALUES ( 'Date'[Calendar Year] ),

        "Sales", CALCULATE ( SUM ( 'Internet Sales'[Sales Amount] ) )

    ), 

    [Sales] > 8000000

)

ORDER BY 'Date'[Calendar Year]

```

```dax


EVALUATE

FILTER (

    SUMMARIZE ( 

        'Internet Sales',

        'Date'[Calendar Year],

        "Sales", SUM ( 'Internet Sales'[Sales Amount] )

    ),

    [Sales] > 8000000

)

ORDER BY 'Date'[Calendar Year]

```

It is important to remember that the formula engine evaluates filters applied to the result of a calculation. When you can, it is better to apply a filter using [[CALCULATE]] or [[CALCULATETABLE]] (see Filtering Data article). However, you have to use [[FILTER]] in this case because the result of an aggregation cannot be part of a filter argument in [[CALCULATE]] or [[CALCULATETABLE]].

[[SUMMARIZE]]

Creates a summary of the input table grouped by the specified columns.

`SUMMARIZE ( <Table> [, <GroupBy_ColumnName> [, [<Name>] [, [<Expression>] [, <GroupBy_ColumnName> [, [<Name>] [, [<Expression>] [, … ] ] ] ] ] ] ] )`

[[ADDCOLUMNS]]

Returns a table with new columns specified by the DAX expressions.

`ADDCOLUMNS ( <Table>, <Name>, <Expression> [, <Name>, <Expression> [, … ] ] )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[DISTINCT]]

Returns a one column table that contains the distinct (unique) values in a column, for a column argument. Or multiple columns with distinct (unique) combination of values, for a table expression argument.

`DISTINCT ( <ColumnNameOrTableExpr> )`

[[LEFT]]

Returns the specified number of characters from the start of a text string.

`LEFT ( <Text> [, <NumberOfCharacters>] )`

[[FILTER]]

Returns a table that has been filtered.

`FILTER ( <Table>, <FilterExpression> )`

[[CALCULATETABLE]]

Context transition

Evaluates a table expression in a context modified by filters.

`CALCULATETABLE ( <Table> [, <Filter> [, <Filter> [, … ] ] ] )`

## Articles in the From SQL to DAX series

- [From SQL to DAX: String Comparison](https://www.sqlbi.com/articles/from-sql-to-dax-string-comparison/)
- [From SQL to DAX: Filtering Data](https://www.sqlbi.com/articles/from-sql-to-dax-filtering-data/)
- *From SQL to DAX: Grouping Data*
- [From SQL to DAX: IN and EXISTS](https://www.sqlbi.com/articles/from-sql-to-dax-in-and-exists/)
- [From SQL to DAX: Joining Tables](https://www.sqlbi.com/articles/from-sql-to-dax-joining-tables/)
- [From SQL to DAX: Implementing NULLIF and COALESCE in DAX](https://www.sqlbi.com/articles/from-sql-to-dax-implementing-nullif-and-coalesce-in-dax/)
- [From SQL to DAX: Projection](https://www.sqlbi.com/articles/from-sql-to-dax-projection/)
