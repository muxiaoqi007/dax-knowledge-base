---
title: "Using visual calculations for conditional formatting"
url: "https://www.sqlbi.com/articles/using-visual-calculations-for-conditional-formatting"
published: "2025-03-12"
updated: 
source: "sqlbi.com"
---

# Using visual calculations for conditional formatting

> 发布：2025-03-12

At SQLBI, we have mixed feelings about visual calculations. On the one hand, several simple calculations can be performed on the visual in a simple way. On the other hand, as soon as the calculation becomes a bit more complex, visual calculations are extremely hard to create – even for seasoned DAX developers.

However, visual calculations are incredibly convenient when it comes to calculations that are specifically tied to a visual. Let us face it: every semantic model contains measures with intricate [[ISINSCOPE]], [[HASONEVALUE]], and [[SELECTEDVALUE]] function calls whose only goal is to determine the color of a font or the background of a cell. An example of the intricacy of those measures is in one of our most viewed articles here: <https://www.sqlbi.com/articles/filtering-the-top-products-alongside-the-other-products-in-power-bi/>.

Visual calculations can be used to control conditional formatting starting with the February 2025 version of Power BI. Several small details must be known to use them, but they are definitely worth learning.

Let us pretend we want to color in red or green the sales amount, based on a comparison of the current value with the average sales amount.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-102.png)

We could obtain this goal using a regular model measure. Creating the measure is an interesting exercise by itself and definitely worth trying because it hides some complexities. In our Contoso model, the *Product[Category]* column is being sorted by *Product[Category Code]*. Give it a try if you like, or just look at the solution:

Measure in Sales table

```dax


Color =

VAR AvgValue =

    AVERAGEX (

        ALLSELECTED ( 'Product'[Category] ),

        CALCULATE ( [Sales Amount], ALLEXCEPT ( 'Product', 'Product'[Category] ) )

    )

VAR Result =

    IF ( [Sales Amount] >= AvgValue, "Green", "Red" )

RETURN

    Result

```

There are a couple of serious drawbacks in this formula: first, the combination of [[CALCULATE]] and [[ALLEXCEPT]] is weird and not easy to find for DAX newbies. Hint: the *Product[Category Code]* column is present in the filter context and is not overridden by the context transition inside [[AVERAGEX]]. Second, the code works by checking the average by category. If a user changes the matrix, and for example uses a different column to slice, the color will be computed inaccurately.

On the other hand, a visual calculation that computes the same color is much easier to write:

Visual calculation

```dax


Color =

VAR AvgValue =

    AVERAGEX ( ROWS, [Sales Amount] )

VAR Result =

    IF ( [Sales Amount] >= AvgValue, "Green", "Red" )

RETURN

    Result

```

Not only is it more straightforward to write, but it also does not contain any reference to *Product[Category]*. Hence, it works on a visual slicing by any column. Moreover, the calculation remains in the visual: it does not pollute the semantic model with business logic that is strictly tied to the current visual.

Using the visual calculation to conditional format a measure hides a small trap because of an incomplete implementation of visual calculations. It is worth remembering that – at the time of writing – visual calculations are still a preview feature; bugs and limitations are totally expected. The data type of visual calculations is not correctly inferred. Therefore, Power BI assumes any visual calculation is a number. However, remember that this issue will be solved in a future version of Power BI Desktop: at that point, you will not have to apply the following workaround.

In our scenario, the *Color* visual calculation is a string. We need to inform Power BI that the data type for the visual calculation is Text.

This can be done in the visual formatting under Format | General | Data Format | Format Options.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-99.png)

By forcing the data type to Text, it is possible to use the visual calculation in the conditional formatting section of *Sales Amount*.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-91.png)

And this is it. *Sales Amount* is now colored the way we want. The visual calculation is tied to the visual but not to the specific column used to slice. Changing from *Product[Category]* to, say, *Product[Brand],* makes the formatting automatically adapt to the new column.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-85.png)

Visual calculations and conditional formatting work together, and it is a match made in heaven. Next time you need a measure to conditional format your report… Give visual calculations a try!

[[ISINSCOPE]]

Returns true when the specified column is the level in a hierarchy of levels.

`ISINSCOPE ( <ColumnName> )`

[[HASONEVALUE]]

Returns true when there’s only one value in the specified column.

`HASONEVALUE ( <ColumnName> )`

[[SELECTEDVALUE]]

Returns the value when there’s only one value in the specified column, otherwise returns the alternate result.

`SELECTEDVALUE ( <ColumnName> [, <AlternateResult>] )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[ALLEXCEPT]]

CALCULATE modifier

Returns all the rows in a table except for those rows that are affected by the specified column filters.

`ALLEXCEPT ( <TableName>, <ColumnName> [, <ColumnName> [, … ] ] )`

[[AVERAGEX]]

Calculates the average (arithmetic mean) of a set of expressions evaluated over a table.

`AVERAGEX ( <Table>, <Expression> )`
