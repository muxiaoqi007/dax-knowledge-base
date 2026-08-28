---
title: "Choosing between DISTINCT and VALUES in DAX"
url: "https://www.sqlbi.com/articles/choosing-between-distinct-and-values-in-dax"
published: "2025-06-30"
updated: 
source: "sqlbi.com"
---

# Choosing between DISTINCT and VALUES in DAX

> 发布：2025-06-30

When you begin modelling in DAX, [[DISTINCT]] and [[VALUES]] often appear interchangeable: both return the list of unique values for a column in the current filter context. In a clean development model, they behave the same, so it is easy to pick one at random – or worse, swap between them without thinking.

However, they are **not** identical. The subtle difference is crucial in production models that may one day contain *invalid relationships* or *bad data*. This article explains:

- The technical behavior of each function.
- Why a single, additional “blank row” makes all the difference.
- A clear rule of thumb for which function to use when.
- Edge‑cases: iterating tables, multiple columns, and statistical measures.

By the end, you will understand why **[[VALUES]] should be your default** and when to use [[DISTINCT]] instead. This article will include non-optimized measures to demonstrate simple cases where the result is different by using [[VALUES]] or [[DISTINCT]]. However, these differences may also appear in optimized code; however, real use cases would be significantly more complex to illustrate in a simple article. Therefore, consider these examples as educational only, and not as best practices for writing the specific calculations shown.

## Differences between [[DISTINCT]] and [[VALUES]]

[[DISTINCT]] and [[VALUES]] are two functions with a similar signature. Both functions have a single parameter that is usually a column reference:

```dax


DISTINCT ( <ColumnNameOrTableExpr> )



VALUES ( <TableNameOrColumnName> )

```

You can see from the signature of the two functions that [[VALUES]] also accepts a table reference, whereas [[DISTINCT]] can receive a more generic table expression. In this initial part of the article, we consider these functions when invoked with a column reference as an argument.

Their behavior appears identical because both functions return a list of unique values from the column that are visible within the current filter context. The only difference is that [[VALUES]] could have one [additional blank row](https://www.sqlbi.com/articles/blank-row-in-dax/) if all ot the following are true:

- The column belongs to a table that is on the one-side of a regular relationship.
- The regular relationship is invalid.
- The filter context does not filter out the additional blank row of the invalid relationship.
- A blank value for the column is not included in the existing data filtered by the filter context.

To further clarify the above list, we must note that if you retrieve values from a column on the one-side of a one-to-many relationship, then the blank value is not allowed in that column. It is a reserved value used to identify the special blank row for the invalid relationship.

A **regular relationship** is a one‑to‑many (or one‑to‑one) link where the one‑side column is a *primary key*. If a value on the many side does not exist on the one side, the relationship becomes **invalid** and the DAX engine silently adds a *blank* row to the one‑side table. All unmatched rows from the many side relate to that blank row. A [**limited relationship**](https://www.sqlbi.com/articles/understanding-blank-row-and-limited-relationships/) (many‑to‑many, bidirectional, etc.) never adds that row, so [[DISTINCT]] and [[VALUES]] behave identically.

## Iterating columns with [[VALUES]]

When you iterate a column with an **iterator**—[[SUMX]], [[AVERAGEX]], [[FILTER]], etc.—you usually want every data point that contributes to the total of the model. If you use [[DISTINCT]], the blank row is skipped, and **all unmatched rows** on the other side of the relationship **are ignored**. With [[VALUES]], you still hit those unmatched rows because the iteration runs once for the blank row as well.

For example, consider the following measure that adjusts the revenues by 1% for countries in Europe:

Measure in Sales table

```dax


Sales Adjusted (incorrect) = 

SUMX ( 

    DISTINCT ( Customer[Continent] ), 

    [Sales Amount] * IF ( Customer[Continent] == "Europe", .99, 1 )

)

```

In a model where there are transactions in *Sales* with a *CustomerKey* value that does not exist in *Customer,* including cases where *Sales[CustomerKey]* is blank, this is the result of a report that shows the incorrect *Sales Adjusted (incorrect)* measure (named “S.A. (incorrect)” in the report) side-by-side with the correct one (*Sales Adjusted*).

![](https://cdn.sqlbi.com/wp-content/uploads/image1-111-scaled.png)

The incorrect amount can be easily explained in the matrix that slices the measures by *Customer[Country]*, because the difference between the two totals can be easily attributed to the initial blank country. However, understanding this difference is significantly more complex when slicing measures by using columns of tables other than *Customer*, such as the matrix that slices the measures by *Product[Brand]*. In this latter case, all the rows present different amounts between the two measures because each row includes unknown customers that are not immediately “visible” in the report.

Here is the right definition of *Sales Adjusted* that computes the correct values:

Measure in Sales table

```dax


Sales Adjusted = 

SUMX ( 

    VALUES ( Customer[Continent] ), 

    [Sales Amount] * IF ( Customer[Continent] == "Europe", .99, 1 )

)

```

By using [[VALUES]] instead of [[DISTINCT]], the additional blank row is included whenever there is an invalid relationship for *Customer*.

## When [[DISTINCT]] is the right choice

There are other calculations where the blank row would lead to misleading the results. Example of this are statistical iterators like [[MINX]], [[MAXX]], [[AVERAGEX]], and percentiles, where grouping all unknown customers into one massive *“Unknown”* customer would create a bias in the statistics. Skipping them entirely may be preferable. Those cases are usually an exception, not the rule. For this reason, we suggest using [[VALUES]] by default, unless you can clearly explain the use case for the use of [[DISTINCT]] in your formula.

For example, this is the incorrect calculation of the average sales amount by *Customer[City]*:

Measure in Sales table

```dax


City Average (incorrect) = 

AVERAGEX ( 

    VALUES ( Customer[City] ), 

    [Sales Amount]

)

```

Once again, a matrix that slices data by a *Customer* attribute, such as *Customer[Country]*, reveals the presence of a blank row that corresponds to a single customer aggregating all the “unknown” customers. This results in a larger, incorrect amount in the total matrix, whereas other reports include incorrect amounts in every cell, such as the matrix that slices measures by *Product[Brand]*.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-108.png)

The correct formula for *City Average* iterates the cities by using [[DISTINCT]] instead of [[VALUES]]:

Measure in Sales table

```dax


City Average = 

AVERAGEX ( 

    DISTINCT ( Customer[City] ), 

    [Sales Amount]

)

```

## Conclusions

[[VALUES]] and [[DISTINCT]] differ by a single blank row, but that row can result in lost revenue, dropped customers, and silent calculation errors once a model encounters real-world data. Adopting **[[VALUES]] as your default iterator driver** (and switching to [[DISTINCT]] only for valid reasons) builds resilience into your measures and saves debugging time in the months that follow.

[[DISTINCT]]

Returns a one column table that contains the distinct (unique) values in a column, for a column argument. Or multiple columns with distinct (unique) combination of values, for a table expression argument.

`DISTINCT ( <ColumnNameOrTableExpr> )`

[[VALUES]]

When a column name is given, returns a single-column table of unique values. When a table name is given, returns a table with the same columns and all the rows of the table (including duplicates) with the [additional blank row](https://www.sqlbi.com/articles/blank-row-in-dax/) caused by an invalid relationship if present.

`VALUES ( <TableNameOrColumnName> )`

[[SUMX]]

Returns the sum of an expression evaluated for each row in a table.

`SUMX ( <Table>, <Expression> )`

[[AVERAGEX]]

Calculates the average (arithmetic mean) of a set of expressions evaluated over a table.

`AVERAGEX ( <Table>, <Expression> )`

[[FILTER]]

Returns a table that has been filtered.

`FILTER ( <Table>, <FilterExpression> )`

[[MINX]]

Returns the smallest value that results from evaluating an expression for each row of a table. Strings are compared according to alphabetical order.

`MINX ( <Table>, <Expression> [, <Variant>] )`

[[MAXX]]

Returns the largest value that results from evaluating an expression for each row of a table. Strings are compared according to alphabetical order.

`MAXX ( <Table>, <Expression> [, <Variant>] )`
