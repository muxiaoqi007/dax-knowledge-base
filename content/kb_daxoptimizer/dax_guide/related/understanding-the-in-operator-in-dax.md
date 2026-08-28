---
title: "Understanding the IN operator in DAX"
url: "https://www.sqlbi.com/articles/understanding-the-in-operator-in-dax"
published: "2022-03-22"
updated: 
source: "sqlbi.com"
---

# Understanding the IN operator in DAX

> 发布：2022-03-22

The IN operator in DAX is useful in multiple scenarios to check whether an expression belongs to a list of values. It is oftentimes used along with the anonymous table constructors. IN is syntax sugar for the [[CONTAINSROW]] function. Just like [[CONTAINSROW]], IN can be used with multiple columns at once although that syntax is not so common.

The most common use of IN is to replace an [[OR]] condition. For example, the following expression can be rewritten with IN:

Measure in Sales table

```dax


RedOrBlack Sales OR :=

CALCULATE ( 

    [Sales Amount],

    Product[Color] = "Red" || Product[Color] = "Black"

)

```

Using IN, you can write the same expression in a more readable way:

Measure in Sales table

```dax


RedOrBlack Sales IN :=

CALCULATE ( 

    [Sales Amount],

    Product[Color] IN { "Red", "Black" }

)

```

Despite this being its most common usage, it is important to note that IN is not a replacement for [[OR]]. Though they yield the very same result, the two expressions have very different semantics. [[OR]] checks whether *Product[Color]* is Red or *Product[Color]* is Black by using Boolean conditions. IN on the other hand checks whether the *Product[Color]* belongs to a temporary table containing Black and Red. The outcome is the same, however the condition is stated in a completely different way. Indeed, with IN you can check values against dynamic tables built through DAX functions, or use anonymous tables by using table constructors. On the other hand, [[OR]] lets you combine conditions involving different columns and expressions. In this article, we focus on IN.

As you will see in the following sections, the IN operator can also be used with dynamic expressions, and it can compare more than one column. The syntax that follows the IN operator in the previous example is a table constructor, and each row can have a row constructor when it contains more than one column.

## [[CONTAINS]], [[CONTAINSROW]]

Even seasoned DAX coders might not have used [[CONTAINS]] or [[CONTAINSROW]] even once in their life. Indeed, these two functions are among the lesser-known functions in DAX. The reason is that despite being useful, they are usually superseded by IN which provides the same functionalities in a more convenient way. We briefly introduce the two functions here, because they provide a good introduction and complement to the IN operator.

[[CONTAINS]] checks that an expression belongs to a table. [[CONTAINS]] requires you to specify a table, a column to look into and the value to look for. The following expression checks whether the *Product* table contains at least one row with the value Red in the Color column:

```dax


EVALUATE

{

    CONTAINS (

        'Product',

        'Product'[Color], "Red"

    )

}

```

[[CONTAINS]] can search into multiple columns. The following query checks whether there is a Contoso branded product which is also red:

```dax


EVALUATE

{

    CONTAINS (

        'Product',

        'Product'[Brand], "Contoso",

        'Product'[Color], "Red"

    )

}

```

[[CONTAINS]] does not require you to specify a value for all the columns in the table. In the previous example, we referenced the entire Product table, but we restricted the search to only the *Product[Color]* and *Product[Brand]* columns.

[[CONTAINSROW]] is the companion function of [[CONTAINS]]. [[CONTAINSROW]] does not require you to specify the column names, because you always need to provide a value for each column in the table. Hence, it presents advantages and disadvantages: you need to write fewer arguments to the function, and at the same time you need to provide a value for each column. Consequently, you need to use [[CONTAINSROW]] with a table that includes only the required columns, like in the following example:

```dax


EVALUATE

{

    CONTAINSROW (

        ALL (

            Product[Brand],

            Product[Color]

        ),

        "Contoso",

        "Red"

    )

}

```

We need to create a temporary table with only the *Product[Brand]* and *Product[Color]* columns, so to be able to use [[CONTAINSROW]] the proper way.

## The IN operator

The IN operator is equivalent to [[CONTAINSROW]]. You can specify the list of values to search for (all columns need to be present) then IN and finally the table to search into. The previous code can be written in a more readable way by using IN:

```dax


EVALUATE

{

    ( "Contoso", "Red" )

        IN ALL (

            Product[Brand],

            Product[Color]

        )

}

```

Please note that since we are searching for two columns, we need to use the full syntax that requires us to enclose different columns of the same row in parentheses. Parentheses are not needed when there is only one column, making the code simpler, like in the following example:

```dax


EVALUATE

{

    "Contoso" IN ALL ( Product[Brand] )

}

```

IN does not require you to specify the column names, as [[CONTAINS]] would. This feature makes it very convenient when used with anonymous tables.

For example, the code we introduced earlier is very readable with IN:

```dax


EVALUATE

{

    CALCULATE (

        [Sales Amount],

        Product[Color] IN { "Red", "Black" } 

    )

}

```

The same code would be awkward if written using [[CONTAINS]], because the developer would need to reference the *[Value]* column in the anonymous table. Besides, apart from it being much harder to read, there is a specific limitation to the use of [[CONTAINS]]: it cannot be used as a [[CALCULATE]] filter. The following code does not work:

```dax


EVALUATE

{

    CALCULATE (

        [Sales Amount],

        CONTAINS ( { "Red", "Black" }, [Value], Product[Color] )

    )

}

```

Therefore, you would be forced to write this code in an even longer way:

```dax


EVALUATE

{

    CALCULATE (

        [Sales Amount],

        FILTER ( ALL ( Product[Color] ), Product[Color] IN { "Red", "Black" } )

    )

}

```

## Using NOT with IN

A very common question is how to combine IN with NOT, to negate the presence of a value in a table. NOT is an operator, exactly like IN is. NOT negates a Boolean expression, therefore the correct way to express the negation is to use IN to build a Boolean expression and then negate it:

```dax


EVALUATE

{

    CALCULATE (

        [Sales Amount],

        NOT ( Product[Color] IN { "Red", "Black" } )

    )

}

```

Because the precedence of NOT is lower than IN’s, the parentheses can be avoided in most scenarios:

```dax


EVALUATE

{

    CALCULATE (

        [Sales Amount],

        NOT Product[Color] IN { "Red", "Black" }

    )

}

```

NOT, in this case, does not operate on the *Product[Color]* column. This is because *Product[Color]* is used as part of the IN operator, which gets applied first due to the precedence rules.

## Using multiple columns

The syntax with parentheses we have used in previous formulas can be used in [[CALCULATE]] too, in order to shorten filters and make the code more readable. As a last example of how one can use the IN operator, look at the following expression:

```dax


EVALUATE

{

    CALCULATE (

        [Sales Amount],

        ( Product[Brand] = "Contoso" && Product[Color] = "Red" )

            || ( Product[Brand] = "Litware" && Product[Color] = "Black" )

    )

}

```

The code computes the sales amount for products that are either Contoso and Red or Litware and Black. Despite working fine, the code is hard to read and to maintain. By leveraging the IN operator, the code can be written in a more convenient way:

```dax


EVALUATE

{

    CALCULATE (

        [Sales Amount],

        ( Product[Brand], Product[Color] )

            IN 

            {

                ( "Contoso", "Red" ),

                ( "Litware", "Black" )

            }

    )

}

```

As you see, we use the parentheses to check whether the pair (Brand, Color) belongs to one of the rows of the anonymous table, again created by using the parentheses to group columns of the same row.

## Conclusion

The IN operator simplifies the DAX syntax required to match a list of values. Though it can be used to compare multiple columns, it is more commonly used with a single column only, for a simpler syntax and a more efficient query execution plan.

[[CONTAINSROW]]

Returns TRUE if there exists at least one row where all columns have specified values.

`CONTAINSROW ( <Table>, <Value> [, <Value> [, … ] ] )`

[[OR]]

Returns TRUE if any of the arguments are TRUE, and returns FALSE if all arguments are FALSE.

`OR ( <Logical1>, <Logical2> )`

[[CONTAINS]]

Returns TRUE if there exists at least one row where all columns have specified values.

`CONTAINS ( <Table>, <ColumnName>, <Value> [, <ColumnName>, <Value> [, … ] ] )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`
