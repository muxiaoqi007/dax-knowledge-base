---
title: "Using RANK instead of RANKX in DAX"
url: "https://www.sqlbi.com/articles/using-rank-instead-of-rankx-in-dax"
published: "2025-12-01"
updated: 
source: "sqlbi.com"
---

# Using RANK instead of RANKX in DAX

> 发布：2025-12-01

In this article, we are not going to discuss the syntax of the [[RANK]] and [[RANKX]] functions. If you need more information, we suggest you consult [DAX Guide](https://dax.guide/) for syntax, as well as the following articles, which introduce both functions: [Introducing the RANK window function in DAX](https://www.sqlbi.com/articles/introducing-the-rank-window-function-in-dax/) and [Introducing RANKX in DAX](https://www.sqlbi.com/articles/introducing-rankx-in-dax/).

[[RANKX]] is the classic method of ranking in DAX; [[RANK]] is a newer window function that works faster, better, and in a more flexible way. [[RANK]] is used in both visual calculations and measures. Which function should you use in which scenario? The answer depends on your requirements: each solution has pros and cons.

If you are interested in a quick answer, [[RANK]] is the preferred function to perform ranking, both in visual calculations and in measures. [[RANKX]] remains slightly more powerful in some complex scenarios, which, to be honest, are relatively rare in the real world. Since [[RANK]] is the preferred choice, the article primarily focuses on the few scenarios where [[RANKX]] remains useful.

Consider the following report, which presents three methods for computing the ranking of brands based on their respective sales amounts. One measure uses [[RANKX]], while the other two use [[RANK]]: one in a measure and the other in a visual calculation. The results remain the same, despite significant differences in the DAX code.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-121.png)

Here is the code of the three different versions:

Measure in Sales table

```dax


Rank =

RANK (

    ALLSELECTED ( 'Product'[Brand] ),

    ORDERBY ( [Sales Amount], DESC )

)

```

Measure in Sales table

```dax


RankX = 

IF (

    ISINSCOPE ( 'Product'[Brand] ),

    RANKX ( ALLSELECTED ( 'Product'[Brand] ), [Sales Amount] )

)

```

Visual Calculation

```dax


Visual Rank = 

IF (

    ISATLEVEL ( [Brand] ),

    RANK ( ROWS, ORDERBY ( [Sales Amount], DESC ) )

)

```

Please note that both *RankX* and *Visual Rank* require an [[IF]] function to prevent the total from being displayed. *Rank* does not, as it blanks values if more than one row is visible in the filter context.

## Visual calculations or measures?

The first choice is between a visual calculation and a measure. Mostly, values shown in Power BI are computed through measures. However, the main disadvantage of using a measure is that you need to hardcode in the measure itself the column over which you are performing the ranking. Both *Rank* and *RankX* require using *[[ALLSELECTED]] ( Product[Brand] )* to identify the table for ranking.

If a user removes the *Product[Brand]* column from the matrix and replaces it with, say, *Product[Color]*, then both measures produce a blank as a result. On the other hand, the visual calculation uses the ROWS keyword to identify the rows in the matrix, regardless of the actual columns used to populate the axis; therefore, it will work with whatever column is used. The only reference to *Product[Brand]* in the visual calculation is in the [[ISATLEVEL]] function call, not in [[RANK]].

![](https://cdn.sqlbi.com/wp-content/uploads/image2-117.png)

Despite letting developers generate code that does not depend specifically on the column used for the ranking, visual calculations suffer from a drawback: a visual calculation can only work on data in the visual. It cannot use data from the model. For example, if one wants to compute the global ranking, regardless of the filters present in the report, a visual calculation is not the right choice. In contrast, both [[RANK]] and [[RANKX]] work well, as they allow developers to specify the table to be used for the ranking. The following two measures perform ranking over [[ALL]] rather than [[ALLSELECTED]]. Therefore, they produce a global ranking, ignoring the presence of filters from slicers and other visuals:

Measure in Sales table

```dax


Rank ALL =

RANK (

    ALL ( 'Product'[Brand] ),

    ORDERBY ( [Sales Amount], DESC )

)

```

Measure in Sales table

```dax


RankX ALL = 

IF (

    ISINSCOPE ( 'Product'[Brand] ),

    RANKX ( ALL ( 'Product'[Brand] ), [Sales Amount] )

)

```

Visual Calculation

```dax


Visual Rank = 

IF (

    ISATLEVEL ( [Brand] ),

    RANK ( ROWS, ORDERBY ( [Sales Amount], DESC ) )

)

```

The *Visual Rank* measure ranks brands from one to five, whereas the two measures maintain the global ranking, as if no filters were applied.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-105.png)

Suppose you need a ranking calculation that is nearly independent from the column being used in the report, and you are ok with the limitation that the ranking needs to be responsive to any filter being applied to the visual. In that case, visual calculations are likely to be your best choice. The main advantage, which should not be underestimated, is their simplicity.

However, if you need more power, you need a measure, and you still need to decide between [[RANK]] and [[RANKX]].

## Choosing between [[RANK]] and [[RANKX]]

[[RANK]] is a window function recently added to DAX. It is flexible, powerful, and easy to use. [[RANKX]] is the classic way of ranking; developers have never shown it too much love, because its syntax and semantics are somewhat intricate.

Currently, [[RANK]] is a better alternative to [[RANKX]] because it is simpler for most tasks. [[RANK]] however is missing some of the features of [[RANKX]]. But these missing features are helpful in such exotic scenarios that they are rarely used.

The main differences between [[RANK]] and [[RANKX]] are:

- [[RANK]] can rank on multiple columns easily by specifying multiple columns in the [[ORDERBY]] section. [[RANKX]] does not have the option of ranking over multiple columns. While it is certainly possible to use [[RANKX]] to rank on multiple columns (see [RANKX on multiple columns with DAX and Power BI](https://www.sqlbi.com/articles/rankx-on-multiple-columns-with-dax-and-power-bi/)), the code is intricate and prone to errors.
- [[RANK]] returns [[BLANK]] if the filter returns more than one row – among other reasons it can return [[BLANK]]. This spares us the need for the conditional logic of [[ISINSCOPE]] or [[HASONEVALUE]], which is needed for [[RANKX]].
- [[RANK]] does not suffer from the random issues [[RANKX]] faces with floating-point values. Indeed, [[RANKX]] may produce an incorrect ranking if the formula used to perform the ranking is a floating-point value. See [Use of RANKX with decimal numbers in DAX](https://www.sqlbi.com/blog/marco/2014/07/16/use-of-rankx-with-decimal-numbers-in-dax/) for more information.
- [[RANK]] has a simpler syntax because most arguments are optional and they can be placed anywhere in the function call. In contrast, [[RANKX]] uses a regular syntax, and arguments are identified by position. Hence, the syntax of [[RANK]] is easier to write than that of [[RANKX]].

Despite being a better solution, [[RANK]] comes with several limitations:

- [[RANK]] uses apply semantics to identify the row to rank against the source table. While this behavior is mostly intuitive, it can be intricate in complex scenarios. This is not a real disadvantage, because it is very rare to find a scenario where [[RANK]] produces counterintuitive results. However, when this happens, it becomes a real brain teaser.
- [[RANK]] lacks the flexibility to rank a value against a lookup table: it can only rank the current row (as determined by apply semantics) against the source table.
- [[RANK]] cannot perform ranking using a source table that contains only extension columns. While this is not a substantial limitation, there may be scenarios where the source table is a variable with no model columns, in which case [[RANK]] is not a good fit.

## [[RANK]] and apply semantics

Apply semantics is a feature used by window functions to determine the current row. If you are not familiar with apply semantics, you can reference the following article: [Understanding apply semantics for window functions in DAX](https://www.sqlbi.com/articles/understanding-apply-semantics-for-window-functions-in-dax/).

The goal of apply semantics is to find the current row in a table. [[RANK]] computes the ranking of the current row relative to a source table sorted in a specific manner. However, what is the current row? Intuitively, if a report is slicing by *Product[Brand]* and we are ranking against *[[ALLSELECTED]] ( Product[Brand] )*, then the current row is the brand that is visible in the current row of the visual. In a more technical sense, it refers to the brand being filtered within the filter context – because each cell in the visual has its own filter context and no row contexts when the measure is evaluated.

Apply semantics inspects the current filter context and tries to use the filters in the filter context to identify a single row in the source table. If the filter context is not selective enough to identify a single row, then [[RANK]] returns blank, because apply semantics would not return a single row.

If [[RANK]] is used in a measure to rank the current row in a visual, it works as expected. Performing the ranking in a calculated column (where there is no filter context, only a row context) is somewhat more problematic. However, the primary use of [[RANK]] is to rank in the current visual. Hence, the apply semantics operates in a relatively intuitive manner.

## Ranking against a lookup table

[[RANKX]] can use two formulas to perform the ranking: one is used during the creation of the lookup table, and the second is used to produce the number for the ranking.

Indeed, the third parameter of [[RANKX]] is an optional expression used to specify the value to be ranked, which can be different from the expression used to build the table. It is most useful when you want to rank an expression based on a lookup table that uses a different expression or value, for example ranking sales against a predefined sales segment table. If you do not specify the third argument, then [[RANKX]] uses the same expression (second argument) for both arguments.

As an example, let us pretend we want to classify sales based on a range table.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-99.png)

Brands with sales exceeding 800,000 should be ranked 1. Brands with sales between 300,000 and 800,000 should be ranked 2, and brands with sales under 300,000, ranked 3. The *Level* measure can be implemented with [[RANKX]]:

Measure in Sales table

```dax


Level = 

RANKX ( 

    'Level',          -- The lookup table source

    'Level'[Limit],   -- The value to build the lookup table

    [Sales Amount]    -- The value to rank

)

```

The second argument, *Level[Limit]*, is used to build the lookup table used to rank the third argument, *[Sales Amount]*. [[RANK]] does not offer a similar feature, because [[RANK]] relies on the apply semantics to determine the current row. In other words, [[RANK]] ranks a row, whereas [[RANKX]] ranks an expression.

The thing is, [[RANKX]] is just one option for computing the *Level* measure, and it is not necessarily the best one. A simple iteration with a filter would produce the same result:

Measure in Sales table

```dax


Level = 

MINX ( 

    FILTER ( 'Level', 'Level'[Limit] <= [Sales Amount] ),

    'Level'[Key]

)

```

A DAX developer is likely to find this second implementation easier and more intuitive.

## Ranking over variables

One scenario where [[RANK]] is not an option is when there is a need to perform ranking over a variable containing only temporary columns. Indeed, [[RANK]] requires at least one column of the source table to be a model column. The reason is – again – apply semantics. Apply semantics requires finding the current row in the source table by inspecting the row context and the filter context.

For example, if the source table contains *[[ALLSELECTED]] ( Product[Brand] )*, then [[RANK]] searches for the values of *Product[Brand]* visible in the current filter context to identify which row to consider for the ranking. If the source table contains only columns with no data lineage, then this matching process cannot be executed, and [[RANK]] returns an error.

For example, the following query correctly produces the list of brands, sales, and their ranking:

Query

```dax


EVALUATE

VAR SourceTable =

    SELECTCOLUMNS (

        ALLSELECTED ( 'Product'[Brand] ),

        "@Brand", 'Product'[Brand],         -- Adding & "" to break the lineage

        "@Sales", [Sales Amount]            -- causes RANK to stop working

    )

RETURN

    SUMMARIZECOLUMNS (

        'Product'[Brand],

        "Sales", [Sales Amount],

        "Rank", RANK ( SourceTable, ORDERBY ( [@Sales] ) )

    )

ORDER BY [Sales]

```

As you can see, the *Rank* column is computed correctly.

![](https://cdn.sqlbi.com/wp-content/uploads/image5-84.png)

However, breaking the lineage of the *@Brand* column by just adding an empty string produces an error:

**[[RANK]]‘s Relation parameter only contains columns added by DAX table functions. This is not supported.**

In such scenario, [[RANKX]] would work fine:

Query

```dax


EVALUATE

VAR SourceTable =

    SELECTCOLUMNS (

        ALLSELECTED ( 'Product'[Brand] ),

        "@Brand", 'Product'[Brand] & "",

        "@Sales", [Sales Amount]        

    )

RETURN

    SUMMARIZECOLUMNS (

        'Product'[Brand],

        "Sales", [Sales Amount],

        "Rank", RANKX ( SourceTable, [@Sales], [Sales Amount], ASC )

    )

ORDER BY [Sales]

```

This last scenario is likely to be the only one where using [[RANKX]] is the preferred solution. As we mentioned, it is not common to need to rank variables containing only temporary columns; therefore, this significantly limits the use of [[RANKX]] over [[RANK]].

## Conclusions

For simple ranking, visual calculations are the most effective approach. They are fast and straightforward, and they naturally produce local ranking, which is the type of ranking most likely to be needed. When visual calculations are not an option, then [[RANK]] is your best friend. It is easier than [[RANKX]], and the features it misses are useful in a very limited number of scenarios.

[[RANKX]] can still be helpful in certain borderline scenarios, but it is mostly superseded by [[RANK]]. Does this mean you need to rush and replace all your measures containing [[RANKX]] with [[RANK]]? Not at all: if a measure works, then there is no need to change it. Unless you are using [[RANKX]] with floating-point values. If that is the case… then [[RANK]] works better and produces safer code.

[[RANK]]

Returns the rank for the current context within the specified partition sorted by the specified order or on the axis specified.

`RANK ( [<Ties>] [, <Relation>] [, <OrderBy>] [, <Blanks>] [, <PartitionBy>] [, <MatchBy>] [, <Reset>] )`

[[RANKX]]

Returns the rank of an expression evaluated in the current context in the list of values for the expression evaluated for each row in the specified table.

`RANKX ( <Table>, <Expression> [, <Value>] [, <Order>] [, <Ties>] )`

[[IF]]

Checks whether a condition is met, and returns one value if TRUE, and another value if FALSE.

`IF ( <LogicalTest>, <ResultIfTrue> [, <ResultIfFalse>] )`

[[ALLSELECTED]]

CALCULATE modifier

Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied inside the query, but keeping filters that come from outside.

`ALLSELECTED ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[ISATLEVEL]]

Report whether the column is present at the current level.

`ISATLEVEL ( <Column> )`

[[ALL]]

CALCULATE modifier

Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied.

`ALL ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[ORDERBY]]

The expressions and order directions used to determine the sort order within each partition. Can only be used within a Window function.

`ORDERBY ( [<OrderBy_Expression> [, [<OrderBy_Direction>] [, <OrderBy_Expression> [, [<OrderBy_Direction>] [, … ] ] ] ] ] )`

[[BLANK]]

Returns a blank.

`BLANK ( )`

[[ISINSCOPE]]

Returns true when the specified column is the level in a hierarchy of levels.

`ISINSCOPE ( <ColumnName> )`

[[HASONEVALUE]]

Returns true when there’s only one value in the specified column.

`HASONEVALUE ( <ColumnName> )`
