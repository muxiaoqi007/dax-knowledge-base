---
title: "Find the products in the top 10 every year with DAX"
url: "https://www.sqlbi.com/articles/find-the-products-in-the-top-10-every-year-with-dax"
published: "2025-11-03"
updated: 
source: "sqlbi.com"
---

# Find the products in the top 10 every year with DAX

> 发布：2025-11-03

We have written and updated a few pieces in the past about how to find the top products, such as [Filtering the top products alongside the other products in Power BI](https://www.sqlbi.com/articles/filtering-the-top-products-alongside-the-other-products-in-power-bi/) and [Filtering the Top 3 products for each category in Power BI](https://www.sqlbi.com/articles/filtering-the-top-3-products-for-each-category-in-power-bi/).

Generally speaking, finding the top products requires using [[GENERATE]] and [[TOPN]]. However, there is an interesting variation of this scenario that solves a specific business problem. Once we have determined the top 10 products by year, we want to filter only those that appear in the top 10 in most years. Obtaining that list of products helps identify evergreen products, that is, the products that remain in the best-seller list consistently.

The following figure shows the top 10 items by year. We highlighted the ones that appear in at least four of the five years:

![](https://cdn.sqlbi.com/wp-content/uploads/image1-120.png)

Obtaining the list (Bottle, Carton, and Tray) is a little more complex because it requires additional table manipulation and some care when creating temporary structures. The result we want to obtain, out of the list of top products, is the following:

![](https://cdn.sqlbi.com/wp-content/uploads/image2-116.png)

For educational purposes, it is helpful to show the whole process of authoring that measure, as some details can be appreciated only by viewing the entire process. Therefore, we follow these steps:

- We create a query to obtain the products, to check and debug the code by looking at the partial results of the query.
- We transform the query into a measure. Here, we focus on making the code work in any filter context.
- We move the code from the measure to a function, to use the same logic in different measures and compute, in the end, both the number of the best products and their respective sales amounts.

The most significant advantage of using a measure is that its product list is dynamic and can be filtered in the report. For example, the following figure shows the ranking of products by year for 2020-2024. Eight products have appeared in the top 10 for at least 4 years, and only one has always been in the top 10: the last one.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-104.png)

## Writing the base query

The algorithm requires a bit of thinking. First, we need to prepare a table containing the top 10 products by year. Having 10 years of data in our demo files, the table will contain approximately 100 rows. This is the first step:

Query

```dax


EVALUATE

VAR NumOfTop = 10

VAR Years =

    SUMMARIZE ( Sales, 'Date'[Year] )

VAR YearsAndTop10 =

    GENERATE (

        Years,

        TOPN ( NumOfTop, VALUES ( 'Product'[ProductKey] ), [Sales Amount] )

    )

RETURN

    YearsAndTop10

```

The result includes two columns: the year and the product key, with 10 product keys per year.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-98.png)

The next step is to group the *YearsAndTop10* table by product and to count, for each product, the number of rows. Because we want to group a variable and not a model table, [[GROUPBY]] is the function to use (see [Differences between GROUPBY and SUMMARIZE](https://www.sqlbi.com/articles/differences-between-groupby-and-summarize/)). For each product key, we want to compute the number of years. We cannot use [[COUNTROWS]] as an aggregation in the [[GROUPBY]] function, because [[GROUPBY]] requires using the [[CURRENTGROUP]] function to iterate over the subset of rows being grouped, and then a [[SUMX]] with a constant value of 1 to obtain the count:

Query

```dax


EVALUATE

VAR NumOfTop = 10

VAR Years =

    SUMMARIZE ( Sales, 'Date'[Year] )

VAR YearsAndTop10 =

    GENERATE (

        Years,

        TOPN ( NumOfTop, VALUES ( 'Product'[ProductKey] ), [Sales Amount] )

    )

VAR ProdsAndCount =

    GROUPBY (

        YearsAndTop10,

        'Product'[ProductKey],

        "NumOfYears", SUMX ( CURRENTGROUP (), 1 )

    )

RETURN

    ProdsAndCount

```

The result of this second step is a table containing the product key and the number of years where the product has appeared.

![](https://cdn.sqlbi.com/wp-content/uploads/image5-83.png)

The final step is to remove from this table the rows where the *NumOfYears* column is not large enough to consider the product as one of the best. This is why we use a variable at the beginning to count the number of years in total, and another variable (*Coverage*) to state that we are interested in products that appear in at least 80% of the total number of years. Using an expression based on these variables in the final [[FILTER]] produces the result we need:

Query

```dax


EVALUATE

VAR NumOfTop = 10

VAR Coverage = 0.8

VAR Years =

    SUMMARIZE ( Sales, 'Date'[Year] )

VAR NumOfYears =

    COUNTROWS ( Years )

VAR YearsAndTop10 =

    GENERATE (

        Years,

        TOPN ( NumOfTop, VALUES ( 'Product'[ProductKey] ), [Sales Amount] )

    )

VAR ProdsAndCount =

    GROUPBY (

        YearsAndTop10,

        'Product'[ProductKey],

        "NumOfYears", SUMX ( CURRENTGROUP (), 1 )

    )

VAR MinYears = NumOfYears * Coverage

VAR BestProds =

    FILTER ( ProdsAndCount, [NumOfYears] >= MinYears )

RETURN

    BestProds

```

The last result of this query, *BestProds*, produces a table with only five rows: the best products appeared in the top 10 for at least 80% of the years.

![](https://cdn.sqlbi.com/wp-content/uploads/image6-76.png)

The first step is completed. We have obtained a query that produces the list of the best products. The next step is to incorporate this code in a measure that uses this list to filter products. As you are about to see, we must consider the presence of the filter context, because the code – so far – runs in a query with no external filter context. However, a measure should work in any filter context.

## Writing the code in a measure

The first trial is to move the query code into a measure, then use the *BestProds* table as a filter in [[CALCULATE]] to compute the number of products:

Measure in Sales table

```dax


Num Best Prods = 

VAR NumOfTop = 10

VAR Coverage = 0.8

VAR Years =

    SUMMARIZE ( Sales, 'Date'[Year] )

VAR NumOfYears =

    COUNTROWS ( Years )

VAR YearsAndTop10 =

    GENERATE (

        Years,

        TOPN ( NumOfTop, VALUES ( 'Product'[ProductKey] ), [Sales Amount] )

    )

VAR ProdsAndCount =

    GROUPBY (

        YearsAndTop10,

        'Product'[ProductKey],

        "NumOfYears", SUMX ( CURRENTGROUP (), 1 )

    )

VAR MinYears = NumOfYears * Coverage

VAR BestProds =

    FILTER ( ProdsAndCount, [NumOfYears] >= MinYears )

VAR Result = CALCULATE ( COUNTROWS ( 'Product' ), BestProds )

RETURN

    Result

```

This measure does not work as intended because it returns 1 for each product, not only for the best ones.

![](https://cdn.sqlbi.com/wp-content/uploads/image7-64.png)

The problem is that the measure is being executed in a filter context that affects multiple variable evaluations. It impacts *Years* (it considers only the currently-filtered sales) and *YearsAndTop10.* Indeed, [[TOPN]] still evaluates *Sales Amount* in the current filter context. There is also another subtle issue inside [[CALCULATE]], but we save that for later. For now, let us solve the first few problems by embedding the variable definition within an [[ALLSELECTED]] to find the best products regardless of any existing filters from the matrix:

Measure in Sales table

```dax


Num Best Prods = 

VAR NumOfTop = 10

VAR Coverage = 0.8

VAR Years =

    CALCULATETABLE (

        SUMMARIZE ( Sales, 'Date'[Year] ),

        ALLSELECTED () 

    )

VAR NumOfYears =

    COUNTROWS ( Years )

VAR YearsAndTop10 =

    CALCULATETABLE (

        GENERATE (

            Years,

            TOPN ( NumOfTop, VALUES ( 'Product'[ProductKey] ), [Sales Amount] )

        ),

        ALLSELECTED () 

    ) 

VAR ProdsAndCount =

    GROUPBY (

        YearsAndTop10,

        'Product'[ProductKey],

        "NumOfYears", SUMX ( CURRENTGROUP (), 1 )

    )

VAR MinYears = NumOfYears * Coverage

VAR BestProds =

    FILTER ( ProdsAndCount, [NumOfYears] >= MinYears )

VAR Result = CALCULATE ( COUNTROWS ( 'Product' ), BestProds )

RETURN

    Result

```

If used in the matrix, the measure now works well.

![](https://cdn.sqlbi.com/wp-content/uploads/image8-53.png)

However, now is the time to deal with that subtle issue we mentioned. *BestProds* contains the *Product[ProductKey]* column that is added to the filter context by [[CALCULATE]]. *Product[ProductKey]* does not conflict with the existing filter context created by the matrix using *Product[Brand]* and *Product[Product Name]*. However, if for any reason a user used the *Product[ProductKey]* column in the report, the result would be incorrect, because the filter placed by *BestProds* would override the filter created by the matrix.

![](https://cdn.sqlbi.com/wp-content/uploads/image9-45.png)

Solving the problem is extremely simple: add [[KEEPFILTERS]] around *BestProds* in [[CALCULATE]] to avoid replacing the outer filter. Here is the final code of the measure:

Measure in Sales table

```dax


Num Best Prods =  

VAR NumOfTop = 10

VAR Coverage = 0.8

VAR Years =

    CALCULATETABLE (

        SUMMARIZE ( Sales, 'Date'[Year] ),

        ALLSELECTED () 

    )

VAR NumOfYears =

    COUNTROWS ( Years )

VAR YearsAndTop10 =

    CALCULATETABLE (

        GENERATE (

            Years,

            TOPN ( NumOfTop, VALUES ( 'Product'[ProductKey] ), [Sales Amount] )

        ),

        ALLSELECTED () 

    )

VAR ProdsAndCount =

    GROUPBY (

        YearsAndTop10,

        'Product'[ProductKey],

        "NumOfYears", SUMX ( CURRENTGROUP (), 1 )

    )

VAR MinYears = NumOfYears * Coverage

VAR BestProds =

    FILTER ( ProdsAndCount, [NumOfYears] >= MinYears )

VAR Result = CALCULATE ( COUNTROWS ( 'Product' ), KEEPFILTERS ( BestProds ) )

RETURN

    Result

```

The measure is somewhat complex. And yet, now that users can see the number of best products, it is likely that they are also interested in the sales volumes for those products. Therefore, the best solution is to create a function that receives the measure to compute as an argument, and then returns the computed measure for only the best products.

## Writing the code in a function

The function requires at least one argument: the expression to evaluate. For educational purposes, we add another argument, the expression to use inside [[TOPN]], to find the best products not only by *Sales Amount,* but also by any other measure:

Function

```dax


Local.ComputeForBestProds = ( computeExpr: EXPR, sortExpr : EXPR ) =>

    VAR NumOfTop = 10

    VAR Coverage = 0.8

    VAR Years =

        CALCULATETABLE (

            SUMMARIZE ( Sales, 'Date'[Year] ),

            ALLSELECTED ( )

        )

    VAR NumOfYears =

        COUNTROWS ( Years )

    VAR YearsAndTop10 =

        CALCULATETABLE ( 

            GENERATE (

                Years,

                TOPN ( NumOfTop, VALUES ( 'Product'[ProductKey] ), CALCULATE ( sortExpr ) )

            ),

            ALLSELECTED ()

        )

    VAR ProdsAndCount =

        GROUPBY (

            YearsAndTop10,

            'Product'[ProductKey],

            "NumOfYears", SUMX ( CURRENTGROUP (), 1 )

        )

    VAR MinYears = NumOfYears * Coverage

    VAR BestProds =

        FILTER ( ProdsAndCount, [NumOfYears] >= MinYears )

    VAR Result = CALCULATE ( computeExpr, KEEPFILTERS ( BestProds ) )

    RETURN

        Result 

```

It is worth noting that *SortExpr*, when used inside [[TOPN]], is surrounded by [[CALCULATE]] to ensure the function works smoothly regardless of the arguments it receives. You can find more information about this technique in the article [Introducing user-defined functions in DAX](https://www.sqlbi.com/articles/introducing-user-defined-functions-in-dax/).

Once the function is created, it is possible to use it in multiple measures, thus avoiding any duplication of code:

Measure in Sales table

```dax


Num Best Prods = 

    Local.ComputeForBestProds ( COUNTROWS ( 'Product' ), [Sales Amount] )

```

Measure in Sales table

```dax


Sales Best Prods = 

    Local.ComputeForBestProds ( [Sales Amount], [Sales Amount] )

```

The two measures share most of the business logic, and they produce nice reports.

![](https://cdn.sqlbi.com/wp-content/uploads/image10-40.png)

## Conclusions

Writing non-trivial DAX code requires an approach that minimizes the possible problems. If a measure requires manipulating tables, the best option is to first define and debug the code in a query. Then, once the business logic is well defined, we move the code into a measure and fix any issues arising from the outer filter context. Once the measure works, if the logic can be used across multiple measures, then defining a function is always a good choice because it lets developers reuse the code.

[[GENERATE]]

The second table expression will be evaluated for each row in the first table. Returns the crossjoin of the first table with these results.

`GENERATE ( <Table1>, <Table2> )`

[[TOPN]]

Returns a given number of top rows according to a specified expression.

`TOPN ( <N_Value>, <Table> [, <OrderBy_Expression> [, [<Order>] [, <OrderBy_Expression> [, [<Order>] [, … ] ] ] ] ] )`

[[GROUPBY]]

Creates a summary the input table grouped by the specified columns.

`GROUPBY ( <Table> [, <GroupBy_ColumnName> [, [<Name>] [, [<Expression>] [, <GroupBy_ColumnName> [, [<Name>] [, [<Expression>] [, … ] ] ] ] ] ] ] )`

[[COUNTROWS]]

Counts the number of rows in a table.

`COUNTROWS ( [<Table>] )`

[[CURRENTGROUP]]

Access to the (sub)table representing current group in GroupBy function. Can be used only inside GroupBy function.

`CURRENTGROUP ( )`

[[SUMX]]

Returns the sum of an expression evaluated for each row in a table.

`SUMX ( <Table>, <Expression> )`

[[FILTER]]

Returns a table that has been filtered.

`FILTER ( <Table>, <FilterExpression> )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[ALLSELECTED]]

CALCULATE modifier

Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied inside the query, but keeping filters that come from outside.

`ALLSELECTED ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[KEEPFILTERS]]

CALCULATE modifier

Changes the CALCULATE and CALCULATETABLE function filtering semantics.

`KEEPFILTERS ( <Expression> )`
