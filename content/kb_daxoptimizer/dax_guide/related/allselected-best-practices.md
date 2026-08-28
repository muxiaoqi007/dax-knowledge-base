---
title: "ALLSELECTED best practices"
url: "https://www.sqlbi.com/articles/allselected-best-practices"
published: "2025-07-14"
updated: 
source: "sqlbi.com"
---

# ALLSELECTED best practices

> 发布：2025-07-14

[[ALLSELECTED]] is among the most complex functions in the whole DAX language. [[ALLSELECTED]] is the only DAX function that leverages shadow filter contexts. Moreover, [[ALLSELECTED]] has a slightly different behavior when used in [[SUMMARIZECOLUMNS]] or inside an iterator. Using [[ALLSELECTED]] with [[SUMMARIZECOLUMNS]] mostly produces the expected result, whereas using [[ALLSELECTED]] inside an iterator can produce weird results. Mixing the two techniques is the perfect recipe for a problematic report!

In this article, we briefly describe [[ALLSELECTED]] features in both scenarios ([[SUMMARIZECOLUMNS]] and iterators), and then we provide the best practices about the function, showing an example where mixing the two behaviors produces an unexpected result.

## Introducing [[ALLSELECTED]]

[[ALLSELECTED]] can be used quite intuitively. For example, consider the requirements for the following report.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-114.png)

The report uses a slicer to filter certain brands. It shows the sales amount of each brand, along with the percentage of each given brand over the total of all selected brands. The percentage formula is straightforward:

```dax


Pct =

DIVIDE (

    [Sales Amount],

    CALCULATE (

        [Sales Amount],

        ALLSELECTED ( 'Product'[Brand] )

    )

)

```

Intuitively, [[ALLSELECTED]] returns the values of the brands selected in the visual filter context – that is, the brands selected between Adventure Works and Proseware. However, Power BI sends to the DAX engine a DAX query that does not have any concept of “current visual.” Therefore, the very concept of visual filter context is not present in DAX measures (visual calculations are a different topic, but they are not involved in regular measures).

How does DAX know about what is selected in the slicer and what is selected in the matrix? The answer is that it does not understand these. [[ALLSELECTED]] does not return the values of a column (or table) filtered outside a visual. What it does is a different task, which, as a side effect, returns the same result most of the time.

*[[ALLSELECTED]]* displays different behaviors, depending on whether it is used in *[[SUMMARIZECOLUMNS]]* or not, and on the presence of shadow filter contexts. Since *[[SUMMARIZECOLUMNS]]* is the primary function used by Power BI to query a semantic model, let us start by showing how *[[ALLSELECTED]]* works within *[[SUMMARIZECOLUMNS]]*.

## [[ALLSELECTED]] in [[SUMMARIZECOLUMNS]]

When used in *[[SUMMARIZECOLUMNS]]*, *[[ALLSELECTED]]* removes filters for the currently-iterated group-by column, restoring the original filter set by *[[SUMMARIZECOLUMNS]]* itself. As an example, let us look at a simplified version of the query that populates the matrix:

```dax


EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Brand],

    TREATAS (

        {

            "Adventure Works",

            "Contoso",

            "Fabrikam",

            "Litware",

            "Northwind Traders",

            "Proseware"

        },

        'Product'[Brand]

    ),

    "Sales_Amount", 'Sales'[Sales Amount],

    "Pct",

        DIVIDE (

            [Sales Amount],

            CALCULATE (

                [Sales Amount],

                ALLSELECTED ( 'Product'[Brand] )

            )

        )

)

```

When executed, the query produces this result.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-110.png)

As you can see from the query, there is no concept of visual filter context. The query uses *[[SUMMARIZECOLUMNS]]* to both place a filter and perform the grouping. The filter is set upfront; then, on each row, *[[SUMMARIZECOLUMNS]]* places a filter for the current brand, so that *Sales Amount* computes the sales of only the current brand. When *[[ALLSELECTED]]* is used, DAX ***removes the filter on the current brand, thereby restoring the filter context that contains the selected brands***.

This technique works fine even though *[[SUMMARIZECOLUMNS]]* does not create the filter. The following query is equivalent to the one we just analyzed, despite the filter being set by *[[CALCULATETABLE]]* rather than by *[[SUMMARIZECOLUMNS]]*:

Query

```dax


EVALUATE

CALCULATETABLE (

    SUMMARIZECOLUMNS (

        'Product'[Brand],

        "Sales_Amount", 'Sales'[Sales Amount],

        "Pct",

            DIVIDE (

                [Sales Amount],

                CALCULATE ( [Sales Amount], ALLSELECTED ( 'Product'[Brand] ) )

            )

    ),

    TREATAS (

        {

            "Adventure Works",

            "Contoso",

            "Fabrikam",

            "Litware",

            "Northwind Traders",

            "Proseware"

        },

        'Product'[Brand]

    )

)

```

Because Power BI uses *[[SUMMARIZECOLUMNS]]* for nearly all the visuals, this mechanism is used the most. However, *[[ALLSELECTED]]* also works in scenarios where *[[SUMMARIZECOLUMNS]]* is not used. In that case, it applies a different technique based on the shadow filter context.

## Introducing shadow filter contexts

Shadow filter contexts are a special type of filter context created by iterators. Every time an iteration starts, the iterator creates a shadow filter context containing the rows of the table being iterated. This filter context is inactive, meaning it does not actively filter any content. It stays dormant until *[[ALLSELECTED]]* possibly uses it.

Let us rewrite the same query we used in the previous section, this time without using *[[SUMMARIZECOLUMNS]]*:

```dax


EVALUATE

CALCULATETABLE (

    ADDCOLUMNS (

        VALUES ( 'Product'[Brand] ),

        "Sales_Amount", [Sales Amount],

        "Pct",

            DIVIDE (

                [Sales Amount],

                CALCULATE (

                    [Sales Amount],

                    ALLSELECTED ( 'Product'[Brand] )

                )

            )

    ),

    TREATAS (

        {

            "Adventure Works",

            "Contoso",

            "Fabrikam",

            "Litware",

            "Northwind Traders",

            "Proseware"

        },

        'Product'[Brand]

    )

)

```

The result of this latter query is identical to the one we examined earlier. However, the reason why *[[ALLSELECTED]]* returns six brands out of all the brands this time, is that *[[ADDCOLUMNS]]*, being an iterator, creates a shadow filter context which *[[ALLSELECTED]]* activates.

Here is a detailed description of the query execution, where we introduce shadow filter contexts in step 3:

1. The outer [[CALCULATETABLE]] creates a filter context with six brands.
2. [[VALUES]] returns the six visible brands and returns the result to [[ADDCOLUMNS]].
3. As an iterator, [[ADDCOLUMNS]] creates a shadow filter context containing the result of [[VALUES]], right before starting the iteration.

- The shadow filter context is like a filter context, but it remains dormant, not affecting the evaluation in any way.
- A shadow filter context can be activated only by [[ALLSELECTED]], as we are about to explain. For now, remember that the shadow filter context contains the six iterated brands.
- We distinguish between a shadow filter context and a regular filter context by calling the latter an explicit filter context.

4. During the iteration, the context transition occurs on one given row. Therefore, the context transition creates a new explicit filter containing solely the iterated brand.
5. When [[ALLSELECTED]] is invoked during the evaluation of the Pct measure, [[ALLSELECTED]] does the following: [[ALLSELECTED]] restores the last shadow filter context on the columns or tables passed as parameters, or on all the columns if [[ALLSELECTED]] has no arguments. Because the last shadow filter context contained six brands, the selected brands become visible again.

This simple example allowed us to introduce the concept of shadow filter context. The previous query shows how [[ALLSELECTED]] leverages shadow filter contexts to retrieve the filter context outside of the current iteration. In early versions of Power BI, before *[[SUMMARIZECOLUMNS]]* was available, Power BI used iterators like *[[ADDCOLUMNS]]* and employed a shadow filter context, rather than the approach seen with *[[SUMMARIZECOLUMNS]]*. As of today, shadow filter contexts are still generated by iterators; however, measures mostly rely on the *SUMMARIZECOLUMN* algorithm. It is essential to recognize that both mechanisms are still in effect; they work together, and if not used carefully, they may yield inaccurate results.

## Learning best practices for [[ALLSELECTED]]

Avoiding problems with *[[ALLSELECTED]]* is relatively simple: never create a measure that uses *[[ALLSELECTED]]* within an iteration, and minimize the use of measures that contain [[ALLSELECTED]] as much as possible. Sooner or later, the reference to *[[ALLSELECTED]]* will be lost in the complexity of the measures, and you will end up using an iterator that invokes a measure that, in turn, invokes another measure using *[[ALLSELECTED]]*. In other words, stay away from using *[[ALLSELECTED]]* if any iterator is active. This ensures that only the mechanism invoked by Power BI at the beginning of the query, using *[[SUMMARIZECOLUMNS]]*, will be active.

Let us examine an example of how numbers can be calculated incorrectly if one does not pay attention and mixes the two systems, simply by using *[[ALLSELECTED]]* within an iteration.

The requirement is to compute the sales of relevant products only. A product is considered relevant if it contributes more than 0.5% of the total sales volume for the current selection. A good option is to use a first measure that computes the total sales, using *[[ALLSELECTED]]* to make it dynamic. And then, use another measure that iterates over the products and applies the conditional logic, adding values only if their share is more than 0.5%:

```dax


Total Sales = CALCULATE ( [Sales Amount], ALLSELECTED () )



Relevant Sales Wrong = 

SUMX (

    'Product',

    IF ( DIVIDE ( [Sales Amount], [Total Sales] ) >= 0.005, [Sales Amount] )

)

```

As its name suggests, the *Relevant Sales Wrong* measure is inaccurate. The reason is that the measure is iterating over *Product*, and from within the iteration, it invokes another measure that internally uses *[[ALLSELECTED]]*. Below is the result of the measure in a matrix.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-98.png)

Many of the rows display a value that exceeds the value in the Total row. Before diving into the details of why the measure is wrong, let us show the correct version of the measure:

```dax


Relevant Sales Correct = 

VAR TotalSales = [Total Sales]

RETURN

SUMX (

    'Product',

    IF ( DIVIDE ( [Sales Amount], TotalSales ) >= 0.005, [Sales Amount] )

)

```

The only difference between the *Relevant Sales Wrong* and *Relevant Sales Correct* measures is that the *Total Sales* measure is invoked outside of the iteration, rather than being inside it. Because the measure changes the filter context by using *[[ALLSELECTED]]*, we may expect the same result for each iterated row: indeed, the measure is not sensitive to the context transition. However, because it uses *[[ALLSELECTED]]*, the difference is very relevant. Below is the same matrix, with the correct measure.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-92.png)

To appreciate the differences, we use a simplified version of the query, where we include the code of both measures:

```dax


DEFINE

    MEASURE Sales[TotalSales] =

        CALCULATE (

            [Sales Amount],

            ALLSELECTED ()

        )



EVALUATE

SUMMARIZECOLUMNS (

    ROLLUPADDISSUBTOTAL (

        'Product'[Brand],

        "Total"

    ),

    "Sales Amount", [Sales Amount],

    "Wrong",

        SUMX (

            'Product',

            --

            --  Inside the iteration, ALLSELECTED restores the shadow filter context

            --  created by SUMX, which is iterating over the products of the given brand

            --

            --  This is the shadow filter context behavior

            --

            IF (

                DIVIDE (

                    [Sales Amount],

                    [TotalSales]

                ) >= 0.005,

                [Sales Amount]

            )

        ),

    "Correct",

        --

        --  Outside of the iteration, ALLSELECTED removes the filter over Product[Brand]

        --  and makes all the selected products visible

        --

        --  This is the SUMMARIZECOLUMNS/ALLSELECTED behavior

        --

        VAR TotalSales = [TotalSales]

        RETURN

            SUMX (

                'Product',

                IF (

                    DIVIDE (

                        [Sales Amount],

                        TotalSales

                    ) >= 0.005,

                    [Sales Amount]

                )

            )

)

```

As you can see in the comments within the DAX query, the *Wrong* calculation calls *TotalSales* from inside the iteration over *Product*. As such, it is using the shadow filter context method. Because *[[SUMX]]* iterates over the products of the given brand, *TotalSales* calculates the sales of all products in the currently-displayed brand.

In the *Correct* expression, the *TotalSales* measure is invoked outside of the iteration. Therefore, there is no shadow filter context, and *[[ALLSELECTED]]* uses the *[[SUMMARIZECOLUMNS]]* method. It removes the filter over *Product[Brand]*, making all products visible – all selected products, in case a slicer was limiting the products.

This example was deliberately simple: one measure using *[[ALLSELECTED]]*, directly called from inside an iteration. In the real world, in a semantic model with hundreds of measures, it is nearly impossible to track every possible use of your measures inside and outside iterators. Hence, the rule is quite simple: *[[ALLSELECTED]]* can be safely used only in measures that are directly placed in the reports. You should refrain from calling a measure if it contains *[[ALLSELECTED]]*, because later on, other measures might call your measure, not knowing that it includes *[[ALLSELECTED]]* as part of the internal calculation.

## Conclusions

[[ALLSELECTED]] is powerful and helpful; there is no need to be intimidated by it. The shadow filter context mechanism was appropriate until several years ago, before the introduction of [[SUMMARIZECOLUMNS]]. When used from inside [[SUMMARIZECOLUMNS]], [[ALLSELECTED]] produces reliable results. However, if a measure involving the use of [[ALLSELECTED]] is invoked from inside an iterator, then the old mechanism takes effect, producing results that are extremely difficult to debug.

The solution is straightforward: ensure that [[ALLSELECTED]] is never used when an iteration begins. This requires paying attention to the chain of measures used, which in turn ensures that your code always produces the expected results.

[[ALLSELECTED]]

CALCULATE modifier

Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied inside the query, but keeping filters that come from outside.

`ALLSELECTED ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[SUMMARIZECOLUMNS]]

Create a summary table for the requested totals over set of groups.

`SUMMARIZECOLUMNS ( [<GroupBy_ColumnName> [, [<FilterTable>] [, [<Name>] [, [<Expression>] [, <GroupBy_ColumnName> [, [<FilterTable>] [, [<Name>] [, [<Expression>] [, … ] ] ] ] ] ] ] ] ] )`

[[CALCULATETABLE]]

Context transition

Evaluates a table expression in a context modified by filters.

`CALCULATETABLE ( <Table> [, <Filter> [, <Filter> [, … ] ] ] )`

[[ADDCOLUMNS]]

Returns a table with new columns specified by the DAX expressions.

`ADDCOLUMNS ( <Table>, <Name>, <Expression> [, <Name>, <Expression> [, … ] ] )`

[[VALUES]]

When a column name is given, returns a single-column table of unique values. When a table name is given, returns a table with the same columns and all the rows of the table (including duplicates) with the [additional blank row](https://www.sqlbi.com/articles/blank-row-in-dax/) caused by an invalid relationship if present.

`VALUES ( <TableNameOrColumnName> )`

[[SUMX]]

Returns the sum of an expression evaluated for each row in a table.

`SUMX ( <Table>, <Expression> )`
