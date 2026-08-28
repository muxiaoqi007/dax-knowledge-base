---
title: "From SQL to DAX: Projection"
url: "https://www.sqlbi.com/articles/from-sql-to-dax-projection"
published: "2020-08-17"
updated: 
source: "sqlbi.com"
---

# From SQL to DAX: Projection

> 发布：2020-08-17

**2020-03-15 UPDATE**: The original version of this article published in 2011 did not mention [[SELECTCOLUMNS]], which was introduced in 2015. This article was rewritten in 2020 to provide updated coverage of projection functions in DAX.

The projection is provided by this classic SELECT in SQL:

```dax


SELECT *

FROM Product

```

It corresponds to this DAX query:

```dax


EVALUATE 'Product'

```

A common projection consists in selecting just a few columns from the source table. For example, the following SQL query only fetches 3 columns from the Product table:

```dax


SELECT [ProductKey], [Product Name], [Unit Price]

FROM Product

```

In DAX you can obtain the same result by using the [[SELECTCOLUMNS]] function, which requires you to specify the column name for each column of the result:

```dax


EVALUATE 

SELECTCOLUMNS (

    'Product',

    "ProductKey", 'Product'[ProductKey],

    "Product Name", 'Product'[Product Name],

    "Unit Price", 'Product'[Unit Price]

)

```

Another common projection in SQL adds one or more columns to all the columns of a table. For example, the following SQL query adds the *Unit* *Margin* column to all the columns of the *Product* table:

```dax


SELECT *, [Unit Price] - [Unit Cost] AS [Unit Margin]

FROM Product

```

You can get the same result in DAX by using the [[ADDCOLUMNS]] function:

```dax


EVALUATE 

ADDCOLUMNS (

    'Product',

    "Unit Margin", 'Product'[Unit Price] - 'Product'[Unit Cost]

)

```

Both [[SELECTCOLUMNS]] and [[ADDCOLUMNS]] keep the duplicated rows included in the original table expression. Applying a [[DISTINCT]] condition can be done in one of two ways. Consider the following SQL query:

```dax


SELECT DISTINCT [ProductKey], [Product Name], [Unit Price]

FROM Product

```

The more efficient way to get the same result is by using the [[SUMMARIZE]] function:

```dax


EVALUATE 

SUMMARIZE (

    'Product',

    'Product'[ProductKey],

    'Product'[Product Name],

    'Product'[Unit Price]

)

```

In case [[SUMMARIZE]] cannot be used over a complex table expression used as first argument instead of the simple *Product* table reference of this example, you could apply [[DISTINCT]] to the result of [[SELECTCOLUMNS]] . However, the following expression should only be used if [[SUMMARIZE]] cannot be used:

```dax


EVALUATE 

DISTINCT (

    SELECTCOLUMNS (

        'Product',

        "ProductKey", 'Product'[ProductKey],

        "Product Name", 'Product'[Product Name],

        "Unit Price", 'Product'[Unit Price]

    )

)

```

By using [[SUMMARIZE]] you cannot change the column names. If you need to rename a column it is advisable to use a [[SELECTCOLUMNS]] consuming the result of a [[SUMMARIZE]], in order to achieve the best possible performance. For example, consider the following SQL query:

```dax


SELECT DISTINCT 

    [ProductKey] AS [Key], 

    [Product Name] AS [Name], 

    [Unit Price] AS [Price]

FROM Product

```

The corresponding DAX version is the following:

```dax


EVALUATE 

SELECTCOLUMNS (

    SUMMARIZE (

        'Product',

        'Product'[ProductKey],

        'Product'[Product Name],

        'Product'[Unit Price]

    ),

    "Key", 'Product'[ProductKey],

    "Name", 'Product'[Product Name],

    "Price", 'Product'[Unit Price]

)

```

Only if [[SUMMARIZE]] cannot be used, should you resort to this alternative, slower technique:

```dax


EVALUATE 

DISTINCT (

    SELECTCOLUMNS (

        'Product',

        "Key", 'Product'[ProductKey],

        "Name", 'Product'[Product Name],

        "Price", 'Product'[Unit Price]

    )

)

```

In general, we always suggest using [[SUMMARIZE]] as an equivalent of SELECT [[DISTINCT]] in SQL, whereas [[ADDCOLUMNS]] and [[SELECTCOLUMNS]] are the DAX functions to use to get a projection without removing duplicated rows in the result.

[[SELECTCOLUMNS]]

Returns a table with selected columns from the table and new columns specified by the DAX expressions.

`SELECTCOLUMNS ( <Table> [[, <Name>], <Expression> [[, <Name>], <Expression> [, … ] ] ] )`

[[ADDCOLUMNS]]

Returns a table with new columns specified by the DAX expressions.

`ADDCOLUMNS ( <Table>, <Name>, <Expression> [, <Name>, <Expression> [, … ] ] )`

[[DISTINCT]]

Returns a one column table that contains the distinct (unique) values in a column, for a column argument. Or multiple columns with distinct (unique) combination of values, for a table expression argument.

`DISTINCT ( <ColumnNameOrTableExpr> )`

[[SUMMARIZE]]

Creates a summary of the input table grouped by the specified columns.

`SUMMARIZE ( <Table> [, <GroupBy_ColumnName> [, [<Name>] [, [<Expression>] [, <GroupBy_ColumnName> [, [<Name>] [, [<Expression>] [, … ] ] ] ] ] ] ] )`

## Articles in the From SQL to DAX series

- [From SQL to DAX: String Comparison](https://www.sqlbi.com/articles/from-sql-to-dax-string-comparison/)
- [From SQL to DAX: Filtering Data](https://www.sqlbi.com/articles/from-sql-to-dax-filtering-data/)
- [From SQL to DAX: Grouping Data](https://www.sqlbi.com/articles/from-sql-to-dax-grouping-data/)
- [From SQL to DAX: IN and EXISTS](https://www.sqlbi.com/articles/from-sql-to-dax-in-and-exists/)
- [From SQL to DAX: Joining Tables](https://www.sqlbi.com/articles/from-sql-to-dax-joining-tables/)
- [From SQL to DAX: Implementing NULLIF and COALESCE in DAX](https://www.sqlbi.com/articles/from-sql-to-dax-implementing-nullif-and-coalesce-in-dax/)
- *From SQL to DAX: Projection*
