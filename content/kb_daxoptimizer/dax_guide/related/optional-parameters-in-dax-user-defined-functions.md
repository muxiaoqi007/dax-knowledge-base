---
title: "Optional parameters in DAX user-defined functions"
url: "https://www.sqlbi.com/articles/optional-parameters-in-dax-user-defined-functions"
published: "2026-06-15"
updated: 
source: "sqlbi.com"
---

# Optional parameters in DAX user-defined functions

> 发布：2026-06-15

When Microsoft announced that DAX User-defined functions (UDFs) are generally available (GA), another new feature was also announced: it is now possible to define optional parameters in a function and assign them default values.

A parameter is optional when the caller can leave it out. In that case, the function still needs a value to work with, so it falls back to a default. DAX provides that default through an expression written directly in the function signature, next to the parameter it belongs to. This is the mechanism we describe in this article.

If you are new to DAX user-defined functions and you want to learn more, please take a look at [our other articles about DAX user-defined functions](https://www.sqlbi.com/dax-user-defined-functions-udf/) before reading any further. Here, we assume you already know how to define a function and call it from a measure or a calculated column. We deliberately use simple functions in the first part of the article, so that the syntax remains the focus; a more practical scenario follows later.

## The syntax of optional parameters

When we declare a parameter, we can add a default expression after the parameter name, followed by its optional type hints, using an equal sign. The general form of a function is the following:

```dax


<FunctionName> =

    ( <ParameterName> [ : <Type> <Subtype> <PassingMode> ] [ = <DefaultExpression> ], ... )

    => <FunctionBody>

```

The only addition compared to a function with mandatory parameters is the default expression, *= <DefaultExpression>*. A parameter with a default expression is optional; one without is mandatory.

Consider the following function, which increments a number. The first parameter, *x*, is mandatory. The second parameter, *y*, is the increment amount and is optional. If it is not specified, the function adds 1:

query

```dax


DEFINE

    FUNCTION Increment = ( x : NUMERIC, y : NUMERIC = 1 ) => x + y

EVALUATE

{

    Increment ( 3 ),        -- Returns 4, the default for y is 1

    Increment ( 10, 20 )    -- Returns 30, y is specified to be 20

}

```

The first call provides only *x*. Because y is omitted, DAX evaluates its default expression, 1, and uses the result as the value of *y*; the function returns 3 + 1. The second call provides both arguments, so the function returns 10 + 20.

When the caller omits an argument, DAX evaluates the corresponding default expression and uses its result as the value of that parameter. The default expression respects the type hints of the parameter and can call other functions, both built-in functions and user-defined functions, but it cannot reference other parameters of the same function.

## Omitting the trailing parameters

A function can have more than one optional parameter. The next function extends *Increment* with a third parameter, *limit*, which caps the result. Both *y* and *limit* are optional. By default, the function increments by 1 and caps the result at 10:

query

```dax


DEFINE

    FUNCTION IncrementLimit =

        (

            x : NUMERIC,

            y : NUMERIC = 1,

            limit : NUMERIC = 10

        ) =>

            MIN ( x + y, limit )



EVALUATE

{

    IncrementLimit ( 1 ),        -- Returns 2, the default for y is 1 and for limit it is 10

    IncrementLimit ( 5, 4 ),     -- Returns 9, y is 4 and the default for limit is 10

    IncrementLimit ( 5, 25, 20 ) -- Returns 20, y is 25 and limit is 20

}

```

The first call provides only *x*: *y* defaults to 1 and *limit* defaults to 10, so the result is *[[MIN]] ( 1 + 1, 10 )*, which is 2. The second call provides *x* and *y* but omits *limit*, so the result is *[[MIN]] ( 5 + 4, 10 )*, which is 9. The third call provides all three arguments, so the result is *[[MIN]] ( 5 + 25, 20 )*, which is 20.

When the parameters you skip are the last ones, you do not need to write anything in their place. You stop the list of arguments earlier, and DAX uses the default expression for every parameter you did not provide. In other words, you only need the separating commas up to the last argument you actually provide; everything after that can be left out.

## Skipping a parameter in the function call

The previous calls always omit parameters from the end of the list. You can also skip a parameter in the middle, while still passing an argument in a later position. To do this, you leave the position empty. You write the comma, but no value before it:

query

```dax


DEFINE

    FUNCTION IncrementLimit =

        (

            x : NUMERIC,

            y : NUMERIC = 1,

            limit : NUMERIC = 10

        ) =>

            MIN ( x + y, limit )



EVALUATE

{

    IncrementLimit ( 5, 25, 20 ), -- Returns 20, y is 25 and limit is 20

    IncrementLimit ( 5, , 20 )    -- Returns 6, the default for y is 1 and limit is 20

}

```

In the second call, the empty position before the comma tells DAX to use the default expression of *y*. The value 20 is assigned to *limit*. The result is therefore *[[MIN]] ( 5 + 1, 20 )*, which is 6.

This syntax is useful when a function has several optional parameters, and you want to set only one of the later ones. The empty position is not pleasant to read, but it is valid; here, leaving the gap is a choice of the caller, not something the function definition imposes.

## Optional parameters should come last

DAX does not require optional parameters to be the last ones in the signature. You can declare a mandatory parameter after an optional one, as in the following function, where *limit* is mandatory but follows the optional *y*:

query

```dax


DEFINE

    FUNCTION IncrementBadPractice =

        (

            x : NUMERIC,

            y : NUMERIC = 1,

            limit : NUMERIC

        ) =>

            MIN ( x + y, limit )



EVALUATE

{

    IncrementBadPractice ( 5, , 20 ) -- Returns 6, the default for y is 1 and limit is 20

}

```

The result is the same as before, 6. The problem is the *IncrementBadPractice* function call, not the result. Because *limit* is mandatory, the caller must always provide it. However, limit comes after the optional *y*, so the only way to provide limit while keeping the default of *y* is to write the empty position: *IncrementBadPractice ( 5, , 20 )*. Here the gap is not a choice; the function forces the caller to write it.

For this reason, we suggest the **best practice**: When you make a parameter optional, make all the following parameters optional as well. The *IncrementLimit* function follows this rule: once *y* is optional, *limit* is optional too. The *IncrementBadPractice* function breaks it, and the cost is awkward code at every call site.

## Detecting missing parameters

So far, every default has been a fixed value. Sometimes there is no natural fixed default, and you want the function to behave differently depending on whether the caller provided the argument at all. You can obtain this behavior by using [[BLANK]] as the default expression and then testing the parameter with [[ISBLANK]] inside the function body.

For example, the following function divides two numbers. The third parameter, *roundingDigits*, controls the number of decimal places. When the caller omits it, the function returns the full-precision result. When the caller provides it, the function rounds the result to the requested number of digits:

query

```dax


DEFINE

    FUNCTION RoundDivision =

        (

            x : NUMERIC,

            y : NUMERIC,

            roundingDigits = BLANK ()

        ) =>

            VAR Result = DIVIDE ( x, y )

            RETURN

                IF (

                    ISBLANK ( roundingDigits ),

                    Result,

                    ROUND ( Result, roundingDigits )

                )



EVALUATE

{

    FORMAT ( RoundDivision ( 2, 3 ), "0.#########" ),    -- Returns 0.666666667, no rounding

    FORMAT ( RoundDivision ( 2, 3, 0 ), "0.#########" ), -- Returns 1, round to 0 digits

    FORMAT ( RoundDivision ( 2, 3, 1 ), "0.#########" ), -- Returns 0.7, round to 1 digit

    FORMAT ( RoundDivision ( 2, 3, 2 ), "0.#########" ), -- Returns 0.67, round to 2 digits

    FORMAT ( RoundDivision ( 2, 3, 3 ), "0.#########" )  -- Returns 0.667, round to 3 digits

}

```

The first call omits *roundingDigits*. Its default expression, [[BLANK]], becomes the value of the parameter, so [[ISBLANK]] returns TRUE and the function returns the unrounded result. The other calls provide the number of digits, so [[ISBLANK]] returns FALSE and the function rounds the result accordingly: zero digits give 1, one digit gives 0.7, two digits give 0.67, and three digits give 0.667.

This pattern is useful when the choice is between doing something and doing nothing. By using [[BLANK]] as the default value, we represent the absence of a value, which the function then interprets.

Be mindful of one limitation: the function cannot distinguish an omitted argument from an argument that is explicitly blank. If the caller writes *RoundDivision ( 2, 3, [[BLANK]] () )*, [[ISBLANK]] returns TRUE exactly as it does for an omitted argument. This is rarely a problem in practice, but it is worth keeping in mind when blank is a legitimate value for the parameter.

## Conclusions

Optional parameters make a user-defined function easier to call in the common case, while still allowing full control when needed. You define an optional parameter by giving it a default expression in the signature. You skip it at call time by omitting the trailing arguments, or by leaving an empty position when you want to keep the default of one parameter while setting a later one.

The default expression is evaluated only when the caller omits the argument; it respects the type hints of the parameter, and it can call other functions. The default values are visible next to the parameters they belong to, which keeps the signature self-documenting.

Be mindful of the order of the parameters. DAX lets you place a mandatory parameter after an optional one, but this should be avoided: every parameter after the first optional one should be optional as well. Otherwise, the caller is forced to write empty positions just to reach a mandatory argument. Follow the rule, and your functions will be both correct and pleasant to call.

[[MIN]]

Returns the smallest value in a column, or the smaller value between two scalar expressions. Ignores logical values. Strings are compared according to alphabetical order.

`MIN ( <ColumnNameOrScalar1> [, <Scalar2>] )`

[[BLANK]]

Returns a blank.

`BLANK ( )`

[[ISBLANK]]

Checks whether a value is blank, and returns TRUE or FALSE.

`ISBLANK ( <Value> )`
