---
title: "From SQL to DAX: Implementing NULLIF and COALESCE in DAX"
url: "https://www.sqlbi.com/articles/from-sql-to-dax-implementing-nullif-and-coalesce-in-dax"
published: "2020-11-10"
updated: 
source: "sqlbi.com"
---

# From SQL to DAX: Implementing NULLIF and COALESCE in DAX

> 发布：2020-11-10

As do many languages, DAX enables people to get the same result through different techniques. However, the same semantic may produce different query plans, so it is a good idea to know different techniques because this could be useful when you want to optimize the performance of a slower expression.

**UPDATE 2020-02-27** : the [[COALESCE]] function is available in DAX, check product version compatibility on [DAX Guide](https://dax.guide). You no longer need to implement the alternative approach described in this article if you have a product supporting [[COALESCE]] . However, this article is still relevant to implement an alternative to NULLIF – which is not implemented in DAX, yet – and for performance optimizations of [[COALESCE]] with equivalent faster DAX expressions.

***DISCLAIMER:** In this article, you will see a technique where [[DIVIDE]] can replace other conditional statements. Though the resulting code is less readable, sometimes there is a performance advantage to using [[DIVIDE]] instead of [[IF]] or [[SWITCH]]. We do not want to describe the technical reasons why [[DIVIDE]] could be more efficient. This depends on several factors. Therefore, you should never assume that [[DIVIDE]] is faster than [[IF]] or [[SWITCH]]; you also should only consider this different way of expressing the same semantic when the performance advantage is measurable and consistent across different queries and tests. Moreover, the DAX engine evolves over time, so any analysis of the differences in query plans could be obsolete very soon. Therefore, do your homework before applying any of these techniques in production, and read [Understanding eager vs. strict evaluation in DAX](https://www.sqlbi.com/articles/understanding-eager-vs-strict-evaluation-in-dax/) for more details about query plans evaluating expressions that could produce blank values.*

## Implementing NULLIF in DAX

The NULLIF function in T-SQL returns a null value if the two specified expressions are equal. The syntax is:

```dax


NULLIF ( <expression1>, <expression2> )

```

NULLIF returns *<**expression1>* if the two expressions are not equal. If the expressions are equal, NULLIF returns a null value of the type of *<expression1>*. This is an equivalent DAX code:

```dax


VAR exp1 = <expression1>

VAR exp2 = <expression2>

RETURN IF ( exp1 == exp2, BLANK(), exp1 )

```

A better version in DAX avoids the use of [[IF]]:

```dax


VAR exp1 = <expression1>

VAR exp2 = <expression2>

RETURN DIVIDE ( exp1, NOT ( exp1 == exp2 ) )

```

Using [[DIVIDE]] instead of an [[IF]] can provide a better execution plan, although this is not guaranteed. In general, whenever we have an [[IF]] statement that uses only two arguments and the result is a numeric value, the [[DIVIDE]] syntax can provide an alternative that is less readable but sometimes faster. The reason for that is described in the following section.

## Considering [[DIVIDE]] instead of [[IF]]

Consider the following DAX pattern where *<expression>* returns a numeric value:

```dax


IF ( <condition>, <expression> )

```

You can get the same result by writing:

```dax


DIVIDE ( <expression>, <condition> )

```

The result of a logical condition is converted to 0 or 1 in DAX. If the condition is false, then the denominator of [[DIVIDE]] is 0 and the result is [[BLANK]]. If the condition is true, then the denominator of [[DIVIDE]] is 1 and the result is identical to *<expression>*.

## Implementing [[COALESCE]] in DAX

The [[COALESCE]] function in ANSI SQL returns the current value of the first expression that does not evaluate to NULL. The syntax is:

```dax


COALESCE ( <expression1>, <expression2>, <expression3>, … )

```

**UPDATE 2020-11-10** : The [[COALESCE]] function in DAX has the same syntax as in SQL.

```dax


COALESCE ( <expression1>, <expression2>, <expression3>, … )

```

[[COALESCE]] does not have a particular optimization, and it corresponds to the following DAX expression:

```dax


VAR exp1 = <expression1>

RETURN IF ( 

    NOT ISBLANK ( exp1 ), 

    exp1, 

    VAR exp2 = <expression2>

    RETURN IF ( 

        NOT ISBLANK ( exp2 ), 

        exp2,

        VAR exp3 = <expression3>

        RETURN IF ( 

            NOT ISBLANK ( exp3 ), 

            exp3,

            …

        )

    )

)

```

A more readable but potentially less efficient way to write the same code is the following:

```dax


VAR exp1 = <expression1>

VAR exp2 = <expression2>

VAR exp3 = <expression3>

…

RETURN SWITCH ( 

    TRUE,

    NOT ISBLANK ( exp1 ), exp1, 

    NOT ISBLANK ( exp2 ), exp2, 

    NOT ISBLANK ( exp3 ), exp3, 

    …

)

```

The code using [[IF]] and [[SWITCH]] works for expressions of any data type. If you are handling numeric expressions and you never want to return a blank, then you can consider this alternative option:

```dax


VAR exp1 = <expression1>

VAR exp2 = <expression2>

VAR exp3 = <expression3>

…

RETURN 

    NOT ISBLANK ( exp1 ) * exp1

    + ISBLANK ( exp1 ) * ( NOT ISBLANK ( exp2 ) ) * exp2

    + ISBLANK ( exp1 ) * ISBLANK ( exp2 ) * ( NOT ISBLANK ( exp3 ) ) * exp3

    …

)

```

In this case, using the multiplication over the result of [[ISBLANK]] produces a similar effect to what we described using [[DIVIDE]] . The difference is that because we are summing different expressions, the result cannot be blank. The propagation of blank in the result can be obtained by wrapping the expression in a [[DIVIDE]] function like in the following expression:

```dax


VAR exp1 = <expression1>

VAR exp2 = <expression2>

VAR exp3 = <expression3>

…

RETURN 

    DIVIDE (

        NOT ISBLANK ( exp1 ) * exp1

        + ISBLANK ( exp1 ) * ( NOT ISBLANK ( exp2 ) ) * exp2

        + ISBLANK ( exp1 ) * ISBLANK ( exp2 ) * ( NOT ISBLANK ( exp3 ) ) * exp3

        …,

        ( NOT ISBLANK ( exp1 ) ) * ( NOT ISBLANK ( exp2 ) ) * ( NOT ISBLANK ( exp3 ) ) * …

)

```

Whenever all the expressions are blank, the denominator of [[DIVIDE]] is 0 and the result is blank.

## Conclusion

The NULLIF and [[COALESCE]] functions available in different versions of SQL have equivalent expressions in DAX. These DAX alternatives are many. Small details in the syntax might have different semantic implications, producing different results. The best practice is to use the simplest possible syntax whenever possible, evaluating more complex DAX expressions only when optimal performance is at stake and making sure you validate that an alternative syntax is faster by doing a proper benchmark.

[[COALESCE]]

Returns the first argument that does not evaluate to a blank value. If all arguments evaluate to blank values, BLANK is returned.

`COALESCE ( <Value1>, <Value2> [, <ValueN> [, … ] ] )`

[[DIVIDE]]

Safe Divide function with ability to handle divide by zero case.

`DIVIDE ( <Numerator>, <Denominator> [, <AlternateResult>] )`

[[IF]]

Checks whether a condition is met, and returns one value if TRUE, and another value if FALSE.

`IF ( <LogicalTest>, <ResultIfTrue> [, <ResultIfFalse>] )`

[[SWITCH]]

Returns different results depending on the value of an expression.

`SWITCH ( <Expression>, <Value>, <Result> [, <Value>, <Result> [, … ] ] [, <Else>] )`

[[BLANK]]

Returns a blank.

`BLANK ( )`

[[ISBLANK]]

Checks whether a value is blank, and returns TRUE or FALSE.

`ISBLANK ( <Value> )`

## Articles in the From SQL to DAX series

- [From SQL to DAX: String Comparison](https://www.sqlbi.com/articles/from-sql-to-dax-string-comparison/)
- [From SQL to DAX: Filtering Data](https://www.sqlbi.com/articles/from-sql-to-dax-filtering-data/)
- [From SQL to DAX: Grouping Data](https://www.sqlbi.com/articles/from-sql-to-dax-grouping-data/)
- [From SQL to DAX: IN and EXISTS](https://www.sqlbi.com/articles/from-sql-to-dax-in-and-exists/)
- [From SQL to DAX: Joining Tables](https://www.sqlbi.com/articles/from-sql-to-dax-joining-tables/)
- *From SQL to DAX: Implementing NULLIF and COALESCE in DAX*
- [From SQL to DAX: Projection](https://www.sqlbi.com/articles/from-sql-to-dax-projection/)
