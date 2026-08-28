---
title: "The COALESCE function in DAX"
url: "https://www.sqlbi.com/articles/the-coalesce-function-in-dax"
published: "2020-08-17"
updated: 
source: "sqlbi.com"
---

# The COALESCE function in DAX

> 发布：2020-08-17

A common requirement in authoring DAX code is to provide a default value for the result of a calculation that might otherwise be blank. The classic approach to solving this requirement is by using an [[IF]] statement. For example, the following code returns the value of either the *Sales[Net Price]* column or the *Sales[Unit Price]* column, whichever is first to not be blank:

```dax


IF ( 

    ISBLANK ( Sales[Net Price] ), 

    Sales[Unit Price], 

    Sales[Net Price] 

)

```

When the expressions considered are measures instead of column references, it is a best practice to save the result of the first measure in a variable. The variable improves the performance by ensuring that the measure is evaluated only once:

```dax


VAR MyResult = [MyMeasure]

VAR DefaultValue = [Default Measure]

VAR Result = 

    IF ( 

        ISBLANK ( MyResult ), 

        DefaultValue, 

        MyResult 

    )

RETURN

    Result

```

The alternative to this expression requires two references to the same *MyMeasure* measure, which could result in a double evaluation of the same expression to obtain the same result. The following expression evaluates *MyMeasure* a first time to test whether its value is blank or not, and then a second time to determine the result of the [[IF]] function if *MyMeasure* returns a non-blank value:

```dax


IF ( 

    ISBLANK ( [MyMeasure] ), 

    [Default Measure], 

    [MyMeasure]

)

```

By using the [[COALESCE]] function the code is more readable. [[COALESCE]] implements the same logic as in the previous examples, evaluating its arguments and returning the first argument that is not a blank:

```dax


COALESCE ( [MyMeasure], [DefaultMeasure] )

```

[[COALESCE]] accepts multiple arguments and returns the first argument that is not blank. If the first argument is blank, [[COALESCE]] returns the value provided by the expression in the second argument, and so on. If all the arguments are blank, [[COALESCE]] returns blank as well.

For example, consider a measure showing the Price of a product, using the average of *Sales[Net Price]* or using the *Product[Unit Price]* column if there are no sales transactions. Without [[COALESCE]], the measure looks like this:

```dax


Price =

VAR AvgPrice =

    AVERAGE ( Sales[Net Price] )

VAR UnitPrice =

    SELECTEDVALUE ( 'Product'[Unit Price] )

VAR Result =

    IF ( 

        ISBLANK ( AvgPrice ), 

        UnitPrice, 

        AvgPrice 

    )

RETURN

    Result

```

By using [[COALESCE]], the code becomes much simpler:

```dax


Price =

VAR AvgPrice =

    AVERAGE ( Sales[Net Price] )

VAR UnitPrice =

    SELECTEDVALUE ( 'Product'[Unit Price] )

VAR Result =

    COALESCE ( AvgPrice, UnitPrice )

RETURN

    Result

```

Performance-wise, [[COALESCE]] implements an [[IF]] condition. For this reason, you should not expect the latter code based on [[COALESCE]] to be faster than the former example based on an [[IF]] . Nevertheless, using [[COALESCE]] improves readability and this is already a great reason to start using it whenever needed. Moreover, the behavior of [[COALESCE]] may be further optimized in future versions of the DAX engine, so that the [[COALESCE]] function also ends up improving performance.

[[IF]]

Checks whether a condition is met, and returns one value if TRUE, and another value if FALSE.

`IF ( <LogicalTest>, <ResultIfTrue> [, <ResultIfFalse>] )`

[[COALESCE]]

Returns the first argument that does not evaluate to a blank value. If all arguments evaluate to blank values, BLANK is returned.

`COALESCE ( <Value1>, <Value2> [, <ValueN> [, … ] ] )`
