---
title: "Differences between INT and CONVERT in DAX"
url: "https://www.sqlbi.com/articles/differences-between-int-and-convert-in-dax"
published: "2022-03-22"
updated: 
source: "sqlbi.com"
---

# Differences between INT and CONVERT in DAX

> 发布：2022-03-22

Writing explicit type conversions is unusual in DAX, because most of the time the implicit conversion happening between different data types in an arithmetic expression provides the results you wanted. However, you might want to enforce a type conversion for different reasons: to round a number or to make sure a certain calculation is always approximated the same way. In particular, the conversion to an integer number can be obtained using different techniques – sometimes with small differences which in borderline cases might produce different results.

What we examine in this article is the difference between two techniques to convert a number to an integer: [[INT]] and [[CONVERT]]. The [[INT]] function has been available in DAX since its first release, whereas [[CONVERT]] was only introduced in 2019. For example, the following instructions convert the number 32.34 into an integer (32):

```dax


INT ( 32.34 )

CONVERT ( 32.34, INTEGER )

```

You may want to know which one of the two is best in terms of performance. We do not think that the difference is relevant; there is a tiny performance advantage in using [[CONVERT]], but it is usually not relevant. The more important difference is at the semantic level: while [[CONVERT]] always returns the requested data type (an [Integer](https://dax.guide/dt/integer/) in our example), [[INT]] returns an integer value in a [Integer](https://dax.guide/dt/integer/) or [Currency](https://dax.guide/dt/currency/) data type, depending on the data type of its argument. If the argument is a floating point data type – which is the [Decimal](https://dax.guide/dt/decimal/) data type in Power BI and DOUBLE in the DAX syntax – then the result of [[INT]] is an [Integer](https://dax.guide/dt/integer/) data type. If the argument is a [Currency](https://dax.guide/dt/currency/) data type – which corresponds to the Fixed Decimal Number in Power BI – then the result is still a [Currency](https://dax.guide/dt/currency/) data type, just rounded to the closest integer value.

If you are wondering when this difference happens to be relevant, take a look at the following example. The query creates two columns that each multiply *Quantity* by *Amount*. The colums are called *Test [[CONVERT]]* and *Test [[INT]]*. To obtain the values you see in these columns, the *Amount* value was rounded to an integer using either [[CONVERT]] or [[INT]]. You see that the result of these two calculations is different in row C.

```dax


DEFINE

    TABLE Test =

        DATATABLE (

            "ID", STRING,

            "Quantity", INTEGER,

            "Amount", CURRENCY,

            {

                { "A", 100000, 5000000000.33 },

                { "B", 150000, 5000000000.33 },

                { "C", 200000, 5000000000.33 }

            }

        )



EVALUATE

ADDCOLUMNS (

    Test,

    "Test CONVERT", Test[Quantity] * CONVERT ( Test[Amount], INTEGER ),

    "Test INT", Test[Quantity] * INT ( Test[Amount] ) 

)

```

| Test[ID] | Test[Quantity] | Test[Amount] | Test CONVERT | Test INT |
| --- | --- | --- | --- | --- |
| A | 100,000 | 5,000,000,000.33 | 500,000,000,000,000 | 500,000,000,000,000.00 |
| B | 150,000 | 5,000,000,000.33 | 750,000,000,000,000 | 750,000,000,000,000.00 |
| C | 200,000 | 5,000,000,000.33 | 1,000,000,000,000,000 | -844,674,407,370,955.10 |

Why is this happening?

The reason is that the data type of *Amount* is [[CURRENCY]] (remember, it corresponds to Fixed Decimal Number in Power BI), so [[INT]] does not change its data type. [[CONVERT]] on the other hand, returns an [Integer](https://dax.guide/dt/integer/). The expected result for C is a large number: 1,000,000,000,000,000, or 1E15. This large number fits within the maximum value that can be represented in an [Integer](https://dax.guide/dt/integer/), that is 9,223,372,036,854,775,807, or 9.22E18. However, the maximum value for [Currency](https://dax.guide/dt/currency/) is 922,337,203,685,477, or 9.22E14, which is smaller than the result expected for row C.

But why are we looking at the result of the multiplication if the conversion happens for the *Amount* values, which is much smaller than the limit of both [Currency](https://dax.guide/dt/currency/) and [Integer](https://dax.guide/dt/integer/)? The reason is that multiplying two [Integer](https://dax.guide/dt/integer/) values produces an [Integer](https://dax.guide/dt/integer/) (as in *Test [[CONVERT]]*), whereas the multiplication between [Currency](https://dax.guide/dt/currency/) and [Integer](https://dax.guide/dt/integer/) produces a [Currency](https://dax.guide/dt/currency/) (as in *Test [[INT]]*). You can find more details in [Understanding numeric data type conversions in DAX](https://www.sqlbi.com/articles/understanding-numeric-data-type-conversions-in-dax/).

When we use the [[INT]] function and pass a [Currency](https://dax.guide/dt/currency/) parameter, the result is still [Currency](https://dax.guide/dt/currency/), so the multiplication with an [Integer](https://dax.guide/dt/integer/) produces a [Currency](https://dax.guide/dt/currency/). If the result of the multiplication exceeds the range of values that can be represented in [Currency](https://dax.guide/dt/currency/), the result is “strange”. Because [Currency](https://dax.guide/dt/currency/) is internally represented with an [Integer](https://dax.guide/dt/integer/), in case of integer overflow we get the least significant representable digits of the result. This is what happens in row C, and the number we get is a negative value!

In this simple example, the error is clearly visible and can be explained. However, when this issue happens in the middle of a complex expression, it could be very hard to understand what is causing an unexpected and apparently random calculation error. For this reason, it is important to choose the data types for numeric columns wisely (see [Choosing Numeric Data Types in DAX](https://www.sqlbi.com/articles/choosing-numeric-data-types-in-dax/)) and pay particular attention to implicit and explicit conversions in your DAX expressions. When you deal with large numbers – also in intermediate values of a long expression – these details can make a big difference!

[[INT]]

Rounds a number down to the nearest integer.

`INT ( <Number> )`

[[CONVERT]]

Convert an expression to the specified data type.

`CONVERT ( <Expression>, <DataType> )`

[[CURRENCY]]

Returns the value as a currency data type.

`CURRENCY ( <Value> )`
