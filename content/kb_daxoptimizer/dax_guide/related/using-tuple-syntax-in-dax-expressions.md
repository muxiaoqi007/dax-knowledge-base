---
title: "Using tuple syntax in DAX expressions"
url: "https://www.sqlbi.com/articles/using-tuple-syntax-in-dax-expressions"
published: "2024-01-15"
updated: 
source: "sqlbi.com"
---

# Using tuple syntax in DAX expressions

> 发布：2024-01-15

The DAX language has a tuple syntax commonly used in table constructors. However, the tuple syntax can also be used whenever you want to describe the combined values for two or more columns – this can be handy when you test the combined values of multiple columns, such as year and month. After reviewing the table constructor syntax, we introduce the tuple syntax and a few examples of where tuples can simplify the DAX code, making it more efficient and easier to read.

## Table constructor syntax

The DAX language has a table constructor syntax to create an unnamed table in a DAX expression. This syntax is helpful when you need a temporary table with a small number of rows in a DAX expression or when you want to create a calculated table with constant and calculated values.

The more common use case for a table constructor is with the IN operator:

```dax


EVALUATE

CALCULATETABLE (

    'Product',

    'Product'[Color] IN { "Red", "Blue", "White" }

)

```

The previous example uses a table constructor that returns one column and three rows. However, the table constructor can have two or more columns for each row, as in the following example:

```dax


EVALUATE

CALCULATETABLE (

    'Product',

    ( 'Product'[Color], 'Product'[Brand] ) 

        IN { ( "Red", "Contoso" ), ( "Blue", "Litware" ) }

)

```

Suppose you need a small table with constant values. In that case, the calculated table with the table constructor is an alternative to the “Enter data” feature in Power BI, which generates a Power Query script with constant values:

Calculated Table

```dax


Price Range = 

SELECTCOLUMNS (

    {

        ( 1, "LOW", 0, 100 ),

        ( 2, "MEDIUM", 100, 500 ),

        ( 3, "HIGH", 500, 9999999 )

    },

    "PriceRangeKey", [Value1],

    "Price Range", [Value2],

    "Min", [Value3],

    "Max", [Value4]

)

```

The advantage of the table constructor in DAX is that it can have dynamic expressions:

Calculated Table

```dax


Price Range Dynamic = 

VAR _MinValue = MIN ( Sales[Net Price] )

VAR _MaxValue = MAX ( Sales[Net Price] )

VAR _StDev = STDEV.P ( Sales[Net Price] )

VAR _Average = AVERAGE ( Sales[Net Price] )

RETURN

SELECTCOLUMNS (

    {

        ( 1, "LOW", _MinValue, _Average - 0.1 * _StDev ),

        ( 2, "MEDIUM", _Average - 0.1 * _StDev, _Average + _StDev ),

        ( 3, "HIGH", _Average + _StDev, _MaxValue )

    },

    "PriceRangeKey", [Value1],

    "Price Range", [Value2],

    "Min", [Value3],

    "Max", [Value4]

)

```

However, the last three examples show that each row is described using parentheses (round brackets), with a value for each column. This syntax is also known as tuple syntax in many programming languages: we do not have an official name in the DAX documentation, so we also use “tuple syntax” in DAX.

## Tuple syntax

A tuple is a sorted list of values separated by commas and delimited by parentheses:

```dax


( 59, "Canada", dt"2024-07-22" )

```

In a table constructor, we use a tuple to describe the content of a row. All the rows in a table constructor must have tuples with the same number of values, which corresponds to the number of columns in the resulting table. Conceptually, the tuple syntax corresponds to the content of a row with one or more columns – but such a row has no column names, so it is not a table: it is just one row of a table.

You can use the tuple syntax whenever there is a DAX expression; the only issue is that a tuple is not a valid expression result. For example, you cannot assign a tuple to a variable:

```dax


VAR XY = ( 2, 18 ) -- Error, invalid syntax

```

However, you can use a tuple with the IN operator:

```dax


( 'Product'[Color], 'Product'[Brand] ) 

    IN { 

        ( "Red", "Contoso" ), 

        ( "Blue", "Litware" ) 

    } 

```

The tuple used with the IN operator enables a simpler syntax for complex logical conditions.

## Using tuples with the IN operator

You may consider the IN operator as an alternative whenever an [[OR]] condition involves two or more columns. For example, consider the following *Holidays Sales Verbose* measure that returns the sales in December 2019 and January 2020:

Measure in Sales table

```dax


Holidays Sales Verbose = 

CALCULATE (

    [Sales Amount],

    OR (

        'Date'[Month] = "December" && 'Date'[Year] = 2019,

        'Date'[Month] = "January" && 'Date'[Year] = 2020

    )

)

```

We can simplify the code by using IN. The columns tested go in the tuple before the IN operator and the valid combination values are described in the table constructor:

Measure in Sales table

```dax


Holidays Sales IN = 

CALCULATE (

    [Sales Amount],

    ( 'Date'[Month], 'Date'[Year] ) IN { ( "December", 2019 ), ( "January", 2020 ) }

)

```

However, when you have a [[CALCULATE]] or [[CALCULATETABLE]] filter, it is better to use the [[TREATAS]] function, which is slightly more efficient than the IN operator:

Measure in Sales table

```dax


Holidays Sales 2019 TREATAS = 

CALCULATE (

    [Sales Amount],

    TREATAS (

        { ( "December", 2019 ), ( "January", 2020 ) },

        'Date'[Month],

        'Date'[Year]

    )

)

```

The performance difference is minimal, so you might prefer the syntax that is easier to read when you have a small model.

Let’s see another example where [[TREATAS]] is not an option. In the following report, each value must be red when it does not include any transaction with a promotion, whereas it must be blue when there is at least one transaction with a promotion.

.

The promotion is a discount for sales of the Contoso brand in the United States. We define a conditional format based on the *Promotion Color* measure defined as follows:

Measure in Sales table

```dax


Promotion Color = 

IF (

    ( "Contoso", "United States" ) 

        IN SUMMARIZE ( Sales, 'Product'[Brand], Store[Country] ),

    "Blue",

    "Red"

)

```

In this case, the [[TREATAS]] function would be more verbose and slower:

Measure in Sales table

```dax


Promotion Color TREATAS = 

IF (

    CALCULATE (

        NOT ISEMPTY ( Sales ),

        KEEPFILTERS ( 

            TREATAS ( 

                { ( "Contoso", "United States" ) },

                'Product'[Brand],

                Store[Country] 

            )

        )

    ),

    "Blue",

    "Red"

)

```

The IN syntax is convenient only when we test a single tuple. With two or more tuples, it is better to use the table constructor as in the following example:

Measure in Sales table

```dax


Promotion Color Multiple = 

IF (

    NOT ISEMPTY (

        INTERSECT ( 

            { 

                ( "Contoso", "United States" ), 

                ( "Adventure Works", "Canada" ) 

            },

            SUMMARIZE ( Sales, 'Product'[Brand], Store[Country] )

        )

    ),

    "Blue",

    "Red"

)

```

## Conclusions

You can use the tuple syntax with the IN operator to simplify the [[OR]] condition syntax that involves multiple columns. The main benefit is readability, mainly when numerous options exist in the [[OR]] condition. When used as a [[CALCULATE]] filter, the [[TREATAS]] syntax provides some performance benefits over the IN operator, but you should balance this with the readability of the resulting code.

[[OR]]

Returns TRUE if any of the arguments are TRUE, and returns FALSE if all arguments are FALSE.

`OR ( <Logical1>, <Logical2> )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[CALCULATETABLE]]

Context transition

Evaluates a table expression in a context modified by filters.

`CALCULATETABLE ( <Table> [, <Filter> [, <Filter> [, … ] ] ] )`

[[TREATAS]]

Treats the columns of the input table as columns from other tables. For each column, filters out any values that are not present in its respective output column.

`TREATAS ( <Expression>, <ColumnName> [, <ColumnName> [, … ] ] )`
