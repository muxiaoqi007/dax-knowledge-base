---
title: "FILTER vs CALCULATETABLE: optimization using cardinality estimation"
url: "https://www.sqlbi.com/articles/filter-vs-calculatetable-optimization-using-cardinality-estimation"
published: "2020-08-17"
updated: 
source: "sqlbi.com"
---

# FILTER vs CALCULATETABLE: optimization using cardinality estimation

> 发布：2020-08-17

Please note that in this article [[CALCULATE]] is used instead of [[CALCULATETABLE]], because they are equivalent ([[CALCULATETABLE]] returns a table, whereas [[CALCULATE]] returns a scalar value). I will extend the article for a deeper discussion. For now, consider this article a discussion about the relative importance of removing explicit [[FILTER]] from the arguments of CALCULATE/CALCULATETABLE, because an implicit [[FILTER]] is always rewritten in case a logical condition is used in one of those arguments.

## How [[FILTER]] Works

The [[FILTER]] function is an iterator. For example, consider the following expression:

```dax
CALCULATE ( 

    SUM ( Movements[Quantity] ),

    FILTER (

        ALL ( 'Date'[Date] ),

        'Date'[Date] <= MAX( 'Date'[Date] )

    )

)
```

[[FILTER]] will iterate all the distinct values of the Date column. Thus, the number of iteration is equal to the number of distinct values of the column. In Adventure Works, this value is 2191.

Now, what happen if you iterate the entire Date table with the following expression?

```dax
CALCULATE ( 

    SUM ( Movements[Quantity] ),

    FILTER (

         ALL ( 'Date' ),

         'Date'[Date] <= MAX( 'Date'[Date] )

     )

 )
```

The number of iterations is the same, because you were iterating the primary key of the Date table – so the cardinality of the Date table is identical to the cardinality of the Date[Date] column. If you look at the query plans with a profiler, you will notice minor differences (just the columns logically included) and, most important, exactly the same VertiPaq SE queries. Remember, what is important is we evaluated a logical condition 2191 times in both expressions.

## How [[CALCULATETABLE]] Works

[[CALCULATETABLE]] pushes more of the computation towards the VertiPaq engine, but remember that each filter argument might contain a [[FILTER]] even if you do not see it. For example, consider this query, where [[CALCULATE]] is used (the behavior is the same with [[CALCULATETABLE]]):

```dax
CALCULATE ( 

    SUM ( Movements[Quantity] ),

    'Date'[Date] < DATE ( 2013, 1, 1)

)
```

It seems faster just because you do not see a [[FILTER]]. In reality, a logical expression in a filter argument of a [[CALCULATE]] statement is always transformed into a corresponding [[FILTER]] statement:

```dax
CALCULATE ( 

    SUM ( Movements[Quantity] ),

    FILTER ( 

        ALL ( 'Date'[Date] ),

        'Date'[Date] < DATE ( 2013, 1, 1)

    )

)
```

This is not so different from the [[FILTER]] we have seen before. However, there are differences in case you have multiple filters on the same table. For example, you can write the following measure two different ways, returning the same result:

```dax
OrdersInPlaceSingleFilter :=

CALCULATE (

    COUNTROWS ( 'Internet Sales' ),

    FILTER (

        ALL ( 'Internet Sales' ),

        'Internet Sales'[Ship Date] >= MAX ( 'Date'[Date] )

        && 'Internet Sales'[Order Date] <= MAX ( 'Date'[Date] )

    )

)



OrdersInPlaceDoubleFilter :=

CALCULATE (

    COUNTROWS ( 'Internet Sales' ),

    FILTER (

        ALL ( 'Internet Sales'[Ship Date] ),

        'Internet Sales'[Ship Date] >= MAX ( 'Date'[Date] )

    ),

    FILTER ( 

        ALL ( 'Internet Sales'[Order Date] ),

        'Internet Sales'[Order Date] <= MAX ( 'Date'[Date] )

    )

)
```

The measure OrdersInPlaceSingleFilter iterates all the 60398 rows in Internet Sales. The measure OrdersInPlaceDoubleFilter executes two filters iterating the 1124 unique values of columns Ship Date and Order Date of the Internet Sales table, for 2248 iterations. The OrdersInPlaceDoubleFilter will be an order of magnitude faster in iterating the filter conditions of the [[CALCULATE]] statement.

Most of the times, you should reduce the number of iterations performed in the filter statements, with just a possible exception. In certain conditions, the filter arguments applied to [[CALCULATE]] might produce a temporary Cartesian product of all the combinations of the elements you are considering. If this happened, the internal evaluation of 1,263,376 combinations would be slower than iterating all the 60,398 rows of Internet Sales. However, this might happen only when different conditions occur simultaneously: you need more than one filter depending on external contexts and you have to evaluate a distinct count measure. For example, in the sample workbook available for download, this measure is very slow:

```dax
ProductsSlow :=

CALCULATE (

    DISTINCTCOUNT ( FactInternetSales[CustomerKey] ),

    FILTER (

        VALUES ( FactInternetSales[OrderDate] ),

        FactInternetSales[OrderDate] >= MIN ( DimDate[FullDateAlternateKey] )

    ),

    FILTER (

        VALUES ( FactInternetSales[ShipDate] ),

        FactInternetSales[ShipDate] <= MAX ( DimDate[FullDateAlternateKey] )

    ),

    FactInternetSalesReason

)
```

We optimized the measure by consolidating the two filters into one, more expensive if considered alone, but producing a better execution plan.

```dax
ProductsOk :=

CALCULATE (

    DISTINCTCOUNT ( FactInternetSales[CustomerKey] ),

    FILTER (

        FactInternetSales,

        FactInternetSales[OrderDate] >= MIN ( DimDate[FullDateAlternateKey] )

        && FactInternetSales[ShipDate] <= MAX ( DimDate[FullDateAlternateKey] )

    ),

    FactInternetSalesReason

)
```

However, please consider that this is a very special condition and the application of a many-to-many relationship (note the FactInternetSalesReason filter) is the factor that – combined with the distinct count measure – produces the worst execution plan. It is correct to assume that separate filters are better, but if you have many filters and a distinct count calculation, you might try to consolidate filters in order to improve performance. I expect this behavior to be improved in future releases of Power Pivot and Analysis Services Tabular. That day, this optimization obtained through a more expensive [[FILTER]] condition will no longer be necessary. In the meantime, always do your benchmark before doing that on your data!

**UPDATE 2018-05-27** You should read the article [[CALCULATE|Filter Arguments in [CALCULATE]]](https://www.sqlbi.com/articles/filter-arguments-in-calculate/), which provides more details about how to improve filters in [[CALCULATE]]. For example, a better implementation of the last code might be this:

```dax


ProductsOk :=

CALCULATE (

    DISTINCTCOUNT ( FactInternetSales[CustomerKey] ),

    KEEPFILTERS ( 

        FILTER (

            ALL ( FactInternetSales[OrderDate], FactInternetSales[ShipDate] ),

            FactInternetSales[OrderDate] >= MIN ( DimDate[FullDateAlternateKey] )

                && FactInternetSales[ShipDate] <= MAX ( DimDate[FullDateAlternateKey] )

        )

    ),

    FactInternetSalesReason

)

```

However, always test whether individual column filters performs better for an [[AND]] condition (several times it is the case).

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

[[AND]]

Checks whether all arguments are TRUE, and returns TRUE if all arguments are TRUE.

`AND ( <Logical1>, <Logical2> )`
