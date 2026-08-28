---
title: "Order of Evaluation in CALCULATE Parameters"
url: "https://www.sqlbi.com/articles/order-of-evaluation-in-calculate-parameters"
published: "2020-08-17"
updated: 
source: "sqlbi.com"
---

# Order of Evaluation in CALCULATE Parameters

> 发布：2020-08-17

In order to fully understand them, you also have to well understand evaluation contexts (row context and filter context).  
The general idea is that these functions transform a row context (if exists) into a filter context, which is automatically propagated to related tables, then modify the filter context according to the parameters passed after the first one, and finally evaluate the expression passed as first parameter in the resulting modified filter context.

**UPDATE 2018-12-26: the article has been updated using [[KEEPFILTERS]] to adapt the existing description to the current behavior in DAX.**

If you read the previous description carefully, you will discover one behavior that is not always intuitive and can be the source of confusion when you start working with DAX. The order of evaluation of the parameters of a function is usually the same as the order of the parameter: the first parameter is evaluated, then the second, then the third, and so on. This is always the case for most of the DAX functions, but not for [[CALCULATE]] and [[CALCULATETABLE]]. In these functions, the first parameter is evaluated only after all the others have been evaluated. If you come from a C# background, you can think to the first parameter as a C# callback function, which will be called only later, when its result will be really required.

Thus, when you write:

```dax
CALCULATE (

    [Measure],

    KEEPFILTERS ( Customer[Country] = "Italy" )

)

```

The [[FILTER]] statement is executed first, and then the [Measure] is executed in a filter context where the Customers visible are only those from Italy (assuming Italy is active in the filter context of the caller of the formula – this is the effect of the [[KEEPFILTERS]] modifier).

This seems pretty intuitive, but things are harder when you have nested [[CALCULATE]] statements. Consider the following example:

```dax
CALCULATE (

    CALCULATE (

        [Measure],

        KEEPFILTERS ( Customer[Country] = "Italy" )

    ),

    ALL ( Customer[Country] )

)

```

In this case, the [[ALL]]( Customer[Country] ) is executed before the inner [[CALCULATE]] statement, so the filter context removes any existing filter existing on the Country column of the Customer table and then applies a filter to that column that has to be equal to Italy. From a functional point of view, the only difference with the previous [[CALCULATE]] formula is that Italy will be the only country selected in evaluating [Measure] *regardless of any filter on Country existing in the filter context of the caller*.

Now consider this other example:

```dax
CALCULATE (

    CALCULATE (

        [Measure],

        ALL ( Customer[Country] )

    ),

    KEEPFILTERS ( Customer[Country] = "Italy" )

)

```

The outer filter over Italy is executed first, and then the [[ALL]] ( Customer[Country] ) removes any of the effects of the external filter, resulting in a [Measure] that will be evaluated in a filter context that has removed any filter over the Country column in the Customer table.

The following example calculates the number of Italian customers who bought something before 2012. Again, the outer filter over Italy is executed first and it applies its effects to the [[FILTER]] function, which is executed in the expression of the outer [[CALCULATE]]. The inner [[CALCULATE]] is executed for each customer and returns the sales of that customer before 2012.

```dax
CALCULATE (

    COUNTROWS (

        FILTER (

            Customer,

            CALCULATE (

                SUM ( Sales[Amount] ),

                YEAR ( Sales[Date] ) < 2012

            ) > 0

        )

    ),

    KEEPFILTERS ( Customer[Country] = "Italy" )

)

```

A possible mistake at this point is to assume that an inversion in evaluation order happens, whereas all the filter parameters of a [[CALCULATE]] are executed independently from each other. In the next expression, the result is the same (Italian customers who bought something before 2012), but the [[FILTER]] operates an iteration over all the customers, and not only the Italian ones, because it is executed in parallel with the filter over Italy.

```dax
CALCULATE (

    COUNTROWS ( Customer ),

    FILTER (

        Customer,

        CALCULATE (

            SUM ( Sales[Amount] ),

            YEAR ( Sales[Date] ) < 2012

        ) > 0

    ),

    KEEPFILTERS ( Customer[Country] = "Italy" )

)

```

By using a nested [[CALCULATE]], we force the execution of the filter over Italy before anything else and then this filter is applied to the [[FILTER]] statement, which calculates the sales only for Italian customers. In this case the result will be the same, but you might observe different performances between the two solutions (the next nested [[CALCULATE]] faster than the previous independent filters), because of the different algorithm that we implemented with the different syntax (even if the results will be the same).

```dax
CALCULATE (

    CALCULATE (

        COUNTROWS ( Customer ),

        FILTER (

            Customer,

            CALCULATE (

                SUM ( Sales[Amount] ),

                YEAR ( Sales[Date] ) < 2012

            ) > 0

        )

    ),

    KEEPFILTERS ( Customer[Country] = "Italy" )

)

```

The conclusion is that the order of execution of [[CALCULATE]] and [[CALCULATETABLE]] parameters is different from other DAX functions and requires you to correctly understand side effects of the filters over the calculation of the complete expression.

[[KEEPFILTERS]]

CALCULATE modifier

Changes the CALCULATE and CALCULATETABLE function filtering semantics.

`KEEPFILTERS ( <Expression> )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[CALCULATETABLE]]

Context transition

Evaluates a table expression in a context modified by filters.

`CALCULATETABLE ( <Table> [, <Filter> [, <Filter> [, … ] ] ] )`

[[FILTER]]

Returns a table that has been filtered.

`FILTER ( <Table>, <FilterExpression> )`

[[ALL]]

CALCULATE modifier

Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied.

`ALL ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`
