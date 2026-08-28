---
title: "Avoiding circular dependency errors in DAX"
url: "https://www.sqlbi.com/articles/avoiding-circular-dependency-errors-in-dax"
published: "2021-02-28"
updated: 
source: "sqlbi.com"
---

# Avoiding circular dependency errors in DAX

> 发布：2021-02-28

A circular dependency is detected whenever two objects reference each other, in such a way that Power BI cannot process the objects. The details of why this happens are outlined in this article: [Understanding Circular Dependencies in Tabular and PowerPivot – SQLBI](https://www.sqlbi.com/articles/understanding-circular-dependencies/). Here, the focus is solely on what to do to solve the scenario. We suggest any interested reader run through the previous article too, because understanding the scenario better is helpful in building more reliable DAX code.

First things first: let us state the obvious. If you create two calculated columns that actually reference each other, then you are generating a circular dependency:

```dax


Line Margin = Sales[Line Amount] - Sales[Discount Pct]

Discount PCt = DIVIDE ( Sales[Line Margin], Sales[Line Amount] )

```

With that said, we are confident that you did not land on this page because of such a trivial error. If that is the case however, even better: you already have your solution. *Line Margin* cannot be computed based on *Discount Pct*, if *Discount Pct* is computed based on *Line Margin*. Rephrase your code and the problem is fixed!

If your code looks correct, that is if you do not see any obvious circular dependencies, then there are two possible causes for a hidden circular dependency:

- You are using context transition inside a calculated column.
- You are creating a relationship that involves either a calculated column or a calculated table.

Let us start analyzing the first scenario: context transition in calculated columns. If you do not pay attention to circular dependencies, you cannot create more than one calculated column in a table – if the formula of the column contains [[CALCULATE]] anywhere. Indeed, [[CALCULATE]] in a calculated column performs a context transition and makes that column dependent on all the columns in the table. If two such columns exist, they depend on each other. Therefore, you experience circular dependency only once you have created the second column.

The correct solution to avoid this is to restrict the list of columns that the calculated column depends on, by using [[ALLEXCEPT]] or [[REMOVEFILTERS]] and keeping only the table’s primary key. If the table has no primary key, then using [[CALCULATE]] in a calculated column is dangerous; this is because context transition is likely to produce unexpected results.

It is worth remembering that [[CALCULATE]] is present whenever you call a measure. Consequently, any calculated column that contains a measure reference should embed the measure call inside a structure like this:

```dax


CALCULATE ( [measure], ALLEXCEPT ( … ) )

```

For example, if you need a calculated column in *Product* to store the sales amount of that product, you should enclose the measure reference inside CALCULATE/ALLEXCEPT this way:

```dax


--  Total Sales is a calculated column of Product

--

--  Do NOT do this

--

Total Sales = [Sales Amount]



--

--  Instead, do this:

--

Total Sales = 

    CALCULATE ( 

        [Sales Amount],

        ALLEXCEPT ( 'Product', 'Product'[ProductKey] )

    )

```

Even though you might not experience a circular dependency error, embedding measures inside CALCULATE/ALLEXCEPT is a best practice. It forces the developer to identify the primary column of the table and guarantees that the calculated column works in any scenario. Indeed, if the table containing the calculated column is on the one-side of a relationship, this technique is not mandatory. Still, it is recommended.

The second scenario where you might experience circular dependencies is when you try to create a one-to-many relationship between two tables and either the column on the many-side is a calculated column, or the table on the many-side is a calculated table that depends on the table on the one-side.

Despite being very different, the same solution applies to both scenarios: you need to make sure that the DAX expression does not contain functions that access the blank row. Therefore, you need to:

- Replace [[VALUES]] with [[DISTINCT]]
- Replace [[ALL]] with [[ALLNOBLANKROW]]

Besides, you must pay attention to many functions that use [[VALUES]] internally. For example, the [[SELECTEDVALUE]] function is converted this way:

```dax


--

--  SELECTEDVALUE is converted internally. 

--  You write this:

--

SELECTEDVALUE ( 'Product'[Color] )



--

--  DAX executes this:

--

IF (

    HASONEVALUE ( 'Product'[Color] ),

    VALUES ( 'Product'[Color] )      -- VALUES generates a dependency on the blank row

)



--

--  You must use this instead:

--

IF (

    HASONEVALUE ( 'Product'[Color] ),

    DISTINCT ( 'Product'[Color] )

)

```

Therefore, if your DAX code contains [[SELECTEDVALUE]], you must expand its definition to be able to replace the internal [[VALUES]] with [[DISTINCT]].

In a similar way, if your code contains a filter argument in [[CALCULATE]], you must expand the filter to the full table syntax to replace the inner [[ALL]]:

```dax


--

--  The filter argument in CALCULATE can be expressed using the compact form.

--  You write this:

--

CALCULATE ( 

    [Sales Amount],

    'Product'[Color] = "Red"

)



--

--  DAX executes this:

--

CALCULATE ( 

    [Sales Amount],

    FILTER ( 

        ALL ( 'Product'[Color] ),   -- ALL generates a dependency on the blank row

        'Product'[Color] = "Red"

    )

)



--

--  You need to use this instead:

--

CALCULATE ( 

    [Sales Amount],

    FILTER ( 

        ALLNOBLANKROW ( 'Product'[Color] ),

        'Product'[Color] = "Red"

    )

)

```

Once you remove all the references to the blank row, the relationship can be created correctly.

## Conclusion

You may be interested in learning more about circular dependencies. A complete technical description of why [[CALCULATE]] enlarges the dependency list in calculated columns, as well as the reason behind the need to remove references to the blank row can both be found in this article: [Understanding Circular Dependencies in Tabular and PowerPivot – SQLBI](https://www.sqlbi.com/articles/understanding-circular-dependencies/).

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[ALLEXCEPT]]

CALCULATE modifier

Returns all the rows in a table except for those rows that are affected by the specified column filters.

`ALLEXCEPT ( <TableName>, <ColumnName> [, <ColumnName> [, … ] ] )`

[[REMOVEFILTERS]]

CALCULATE modifier

Clear filters from the specified tables or columns.

`REMOVEFILTERS ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[VALUES]]

When a column name is given, returns a single-column table of unique values. When a table name is given, returns a table with the same columns and all the rows of the table (including duplicates) with the [additional blank row](https://www.sqlbi.com/articles/blank-row-in-dax/) caused by an invalid relationship if present.

`VALUES ( <TableNameOrColumnName> )`

[[DISTINCT]]

Returns a one column table that contains the distinct (unique) values in a column, for a column argument. Or multiple columns with distinct (unique) combination of values, for a table expression argument.

`DISTINCT ( <ColumnNameOrTableExpr> )`

[[ALL]]

CALCULATE modifier

Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied.

`ALL ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[ALLNOBLANKROW]]

CALCULATE modifier

Returns all the rows except blank row in a table, or all the values in a column, ignoring any filters that might have been applied.

`ALLNOBLANKROW ( <TableNameOrColumnName> [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[SELECTEDVALUE]]

Returns the value when there’s only one value in the specified column, otherwise returns the alternate result.

`SELECTEDVALUE ( <ColumnName> [, <AlternateResult>] )`
