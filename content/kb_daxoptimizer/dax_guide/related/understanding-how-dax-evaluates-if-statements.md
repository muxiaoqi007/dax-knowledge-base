---
title: "Understanding how DAX evaluates IF statements"
url: "https://www.sqlbi.com/articles/understanding-how-dax-evaluates-if-statements"
published: "2025-06-17"
updated: 
source: "sqlbi.com"
---

# Understanding how DAX evaluates IF statements

> 发布：2025-06-17

DAX is a functional language. This means that – no matter how complicated it is – a measure is just ONE function call. Then, functions call other functions, creating the intricacies of a sophisticated DAX expression. However, there is always just one function at the top level. This is, at the same time, beautiful and painful, elegant and complex to understand. It is fair to say that being functional is what makes DAX so fascinating.

However, when a DAX formula is executed, it loses its functional nature. Indeed, in the end it needs to be transformed into a set of simpler queries executed by one of the engines of DAX: either the storage engine or the formula engine. During this step, the function execution is transformed, and it becomes much simpler.

## Introducing calculation requirements

Mostly, we do not worry too much about how DAX works… Here is the 10,000 foot view of the creation of a DAX report for most DAX developers.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-110.png)

As simplistic as this picture may seem, it is interesting to go deeper and better understand what happens when measures are automagically transformed into numbers. In this article, we focus on the [[IF]] statement, probably one of the most commonly-used functions in DAX. As you are about to find out, it is not as intuitive as it may seem.

The report includes two measures: *Sales Amount* and *Discounted Sales*. The idea of *Discounted Sales* is to reduce by 5% the value of *Sales Amount* if it is larger than 1 million:

Measure in Sales table

```dax


Sales Amount = SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

```

Measure in Sales table

```dax


Discounted Sales = 

IF ( 

    [Sales Amount] >= 1E6,

    [Sales Amount] * 0.95,

    [Sales Amount] 

)

```

The report slices measures by *Product[Brand]*. Hence, a natural way of thinking about how DAX will execute the query is the following:

*Compute the sales amount sliced by brand and then execute the [[IF]] statement on each line, checking whether the amount is greater than one million. If that is true, then the result is the sales amount itself. Otherwise, compute the sales amount minus 5% and produce that as the result.*

In other words, the [[IF]] statement is executed once per brand during an iteration.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-107.png)

Despite this being intuitive, it is not how things actually go. Moreover, this incorrect perception leads to considerable confusion when computing the totals. Indeed, this iterative process leads to a second incorrect perception: the total is computed by summing the values calculated for each brand. We elaborated on this common mistake here: <https://www.sqlbi.com/articles/why-power-bi-totals-might-seem-inaccurate/>.

## Analyzing the DAX execution

The goal of DAX is to be fast. This means reducing the number of queries being executed and adopting strategies that statistically run better. In the formula we wrote, we used *Sales Amount* in both branches of the [[IF]] statement. However, there are many scenarios where the two formulas in the two branches differ significantly, involving different measures. Every time DAX computes the *Sales Amount* measure, it scans the *Sales* table, which is potentially a heavy operation. Therefore, the engine attempts to minimize the number of scans for *Sales*.

An [[IF]] function has three arguments: the “if” condition, the “then” expression, and the “else” expression.

To obtain its result, [[IF]] performs these steps:

- Compute the ingredients of the [[IF]] condition (*Sales Amount* in this case) for all brands.
- Divide the brands into two categories: one where the [[IF]] condition is true, and another where it is false. In our example, the “then” bucket contains the brands whose *Sales Amount* is greater than or equal to one million, and the “else” bucket contains the brands whose *Sales Amount* is less than one million.
- Compute the “then” expression of [[IF]] for the “then” bucket.
- Compute the “else” expression for the “else” bucket.
- Combine the results and produce the output.

It is helpful to verify this by examining the execution plan of the query.

The query being executed by Power BI to prepare the report is similar to the following, simplified query:

Query

```dax


EVALUATE

SUMMARIZECOLUMNS (

    ROLLUPADDISSUBTOTAL ( 'Product'[Brand], "IsGrandTotalRowTotal" ),

    "Sales Amount", [Sales Amount],

    "Discounted Sales", [Discounted Sales]

)

```

When executed, it shows five different storage engine queries, one of which we can ignore (line 6 returns the list of brands in *Product*). The first retrieves the sales amount by *Product[Brand]*.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-97.png)

The second step, that is, checking the [[IF]] condition, is executed by the formula engine. The formula engine identifies which brands meet the condition and which do not. Then it uses this information to execute the following two storage engine queries, where you can notice, in the “where” part, that the first one retrieves the “then” bucket and the second one retrieves the “else” bucket. This is the “then” storage engine query:

```dax


WITH

    $Expr0 := ( PFCAST ( 'Sales'[Quantity] AS INT ) * PFCAST ( 'Sales'[Net Price] AS INT ) ) 

SELECT

    'Product'[Brand],

    SUM ( @$Expr0 )

FROM 'Sales'

    LEFT OUTER JOIN 'Product'

        ON 'Sales'[ProductKey]='Product'[ProductKey]

WHERE

    'Product'[Brand] IN ( 'Contoso', 'Adventure Works', 'The Phone Company' ) ;

```

And this is the “else” query:

```dax


WITH

    $Expr0 := ( PFCAST ( 'Sales'[Quantity] AS INT ) * PFCAST ( 'Sales'[Net Price] AS INT ) ) 

SELECT

    'Product'[Brand],

    SUM ( @$Expr0 )

FROM 'Sales'

    LEFT OUTER JOIN 'Product'

        ON 'Sales'[ProductKey]='Product'[ProductKey]

WHERE

    'Product'[Brand] IN ( 'Wide World Importers', 'Northwind Traders', 'Southridge Video',

        'Litware', 'Fabrikam', 'Proseware', 'A. Datum', 'Tailspin Toys' ) ;

```

The last storage engine query retrieves the Sales Amount with no slicing by brand, and this is useful for the total of the matrix:

```dax


WITH

    $Expr0 := ( PFCAST ( 'Sales'[Quantity] AS INT ) * PFCAST ( 'Sales'[Net Price] AS INT ) ) 

SELECT

    SUM ( @$Expr0 )

FROM 'Sales';

```

You can verify this behavior by experimenting with variations of the formula in your model; it greatly helps in understanding precisely how DAX executes the [[IF]] function.

Although this algorithm is very effective in more general scenarios, it requires three scans of the *Sales* table (plus one for the total). As code developers, we know that this can be accomplished with a single scan because we use *Sales Amount* in both branches of the [[IF]] statement. If we want to improve this code, we can help DAX by using a variable:

Measure in Sales table

```dax


Discounted Sales 2 = 

VAR SalesAmount = [Sales Amount]

RETURN

    IF ( 

        SalesAmount >= 1E6,

        SalesAmount * 0.95,

        SalesAmount

    )

```

By using the variable, we clearly state that we want to compute the *Sales Amount* measure for all brands, regardless of whether the [[IF]] condition is present. DAX uses this information to change the query plan, executing the [[IF]] condition in the formula engine for each brand, using the *Sales Amount* available. As you can see, there is now a single storage engine query that aggregates *Sales Amount* by brand instead of the two we had earlier. Note that the second storage engine query in this screenshot corresponds to the last storage engine query of the previous example, the one for the total.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-91.png)

Be mindful that in this specific scenario, using a variable helps in reducing the number of scans. This does not mean that it is always a good practice. Depending on multiple factors, such as the complexity of the measure, the size of your model, and the number of values in the columns used for grouping, one solution may be more suitable than another. Improving your DAX code always requires extensive testing.

## Conclusions

In this article, we have seen how [[IF]] is executed when used in a measure as one of the top-level functions. Things may be different when [[IF]] is used inside an iteration or in more complex scenarios. If you are curious about these topics, we have just the training course for you: Optimizing DAX, which provides a deep analysis of DAX execution from a performance perspective.

The more you learn about how DAX executes your code, the better a DAX developer you become. Indeed, rather than believing that “Magic Happens,” you will have a profound understanding of the DAX internals, in turn refining your skills and writing better and better DAX code.

[[IF]]

Checks whether a condition is met, and returns one value if TRUE, and another value if FALSE.

`IF ( <LogicalTest>, <ResultIfTrue> [, <ResultIfFalse>] )`
