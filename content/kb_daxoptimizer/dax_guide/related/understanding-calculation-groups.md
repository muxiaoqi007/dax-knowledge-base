---
title: "Understanding Calculation Groups"
url: "https://www.sqlbi.com/articles/understanding-calculation-groups"
published: "2020-08-17"
updated: 
source: "sqlbi.com"
---

# Understanding Calculation Groups

> 发布：2020-08-17

There are two entities to consider: calculation groups and calculation items. A calculation group is a collection of calculation items, grouped together based on a user-defined criterion. For both calculation groups and calculation items, there are properties that the developer must set correctly.

A **calculation group** is a simple entity, defined by:

- The calculation group **Name**. This is the name of the table that represents the calculation group on the client side.
- The calculation group **Precedence**. When there are multiple active calculation groups, a number that defines the precedence used to apply each calculation group to a measure reference.
- The calculation group attribute **Name**. This is the name of the column that includes the calculation items, displayed to the client as unique items available in the column.

A **calculation item** is a much more sophisticated entity, and here is the list of its properties:

- The calculation item **Name**. This becomes one value of the calculation group column. Indeed, a calculation item is like one row in the calculation group table.
- The calculation item **Expression**. A DAX expression that might contain special functions like [[SELECTEDMEASURE]] . This is the expression that defines how to apply the calculation item.
- The sort order of the calculation item is defined by the **Ordinal** value. This property defines how the different calculation items are sorted when presented to the user. It is very similar to the sort-by-column feature of the data model.
- **Format String Expression**. If not specified, a calculation item inherits the format string of its base measure. Nevertheless, if the modifier changes the calculation, then it is possible to override the measure format string with the format of the calculation item.

The *Format String Expression* property is important in order to obtain a consistent behavior of the measures in the model according to the calculation item being applied to them. For example, consider the following calculation group containing two calculation items for time intelligence: year-over-year (**YOY**) is the difference between a selected period and the same period in the previous year; year-over-year percentage (**YOY%**) is the percentage of **YOY** over the amount in the same period in the previous year:

```dax


-- 

-- Calculation Item: YOY

-- 

    VAR CurrYear =

        SELECTEDMEASURE ()

    VAR PrevYear =

        CALCULATE ( 

            SELECTEDMEASURE (), 

            SAMEPERIODLASTYEAR ( 'Date'[Date] ) 

        )

    VAR Result = 

        CurrYear - PrevYear

    RETURN Result



-- 

-- Calculation Item: YOY%

-- 

    VAR CurrYear =

        SELECTEDMEASURE ()

    VAR PrevYear =

        CALCULATE ( 

            SELECTEDMEASURE (), 

            SAMEPERIODLASTYEAR ( 'Date'[Date] ) 

        )

    VAR Result =

        DIVIDE ( 

            CurrYear - PrevYear, 

            PrevYear 

        )

    RETURN Result

```

The result produced by these two calculation items in a report is correct, but if the *Format String Expression* property does not override the default format string, then **YOY%** is displayed as a decimal number instead of a percentage.

![](https://cdn.sqlbi.com/wp-content/uploads/Calculation-groups-2-Understanding-01.png)

The example displays the **YOY** evaluation of the *Sales Amount* measure using the same format string as the original *Sales Amount* measure. This is the correct behavior to display a difference. However, the **YOY%** calculation item displays the same amount as a percentage of the value of the previous year. The number shown is correct, but for January one would expect to see 12% instead of 0.12. In this case the expected format string should be a percentage, regardless of the format of the original measure. To obtain the desired behavior, set the *Format String Expression* property of the **YOY%** calculation item to percentage, overriding the behavior of the underlying measure.

![](https://cdn.sqlbi.com/wp-content/uploads/Calculation-groups-2-Understanding-02.png)

If the *Format String Expression* property is not assigned to a calculation item, the existing format string is used.

The format string can be defined using a fixed format string or in more complex scenarios by using a DAX expression that returns the format string. When writing a DAX expression, it is possible to refer to the format string of the current measure using the [[SELECTEDMEASUREFORMATSTRING]] function, which returns the format string currently defined for the measure. For example, if the model contains a measure that returns the currently selected currency and you want to include the currency symbol as part of the format string, you can use this code to append the currency symbol to the current format string:

```dax


SELECTEDMEASUREFORMATSTRING () & " " & [Selected Currency]

```

Customizing the format string of a calculation item is useful to preserve user experience consistency when browsing the model. However, a careful developer should consider that the format string operates on any measure used with the calculation item. When there are multiple calculation groups in a report, the result produced by these properties also depends on the calculation group precedence.

## Introducing calculation item application

The details of how a calculation item is applied are quite intricate. This article introduces the topic and provide some best practices. A following article will describe the topic in depth with more complex examples.

Calculation items can be applied by the user using, for example, a slicer, or by using [[CALCULATE]] to filter the calculation group. For example, consider the following calculation item:

```dax


-- 

-- Calculation Item: YTD

--

    CALCULATE ( 

        SELECTEDMEASURE (), 

        DATESYTD ( 'Date'[Date] ) 

    )

```

In order to apply the calculation item in an expression, you filter the calculation group:

```dax


CALCULATE (

    [Sales Amount], 

    'Time Intelligence'[Time calc] = "YTD"

)

```

There is nothing magical about calculation groups: They are tables, and as such they can be filtered by [[CALCULATE]] like any other table. When [[CALCULATE]] applies a filter to a calculation item, DAX uses the definition of the calculation item to rewrite the expression before evaluating it.

Therefore, based on the definition of the calculation item, the previous code is interpreted as follows:

```dax


CALCULATE (

    CALCULATE ( 

        [Sales Amount], 

        DATESYTD ( 'Date'[Date] ) 

    ) 

)

```

The application of a calculation item replaces a measure reference with the expression of the calculation item, still applying an implicit context transition. Focus your attention on this sentence: *A measure reference is replaced.* Without a measure reference, a calculation item does not apply any modification. For example, the following code is not affected by any calculation item because it does not contain any measure reference:

```dax


CALCULATE (

    SUMX ( Sales, Sales[Quantity] * Sales[Net Price] ), 

    'Time Intelligence'[Time calc] = "YTD"

)

```

In the previous example, the calculation item does not perform any transformation because the code inside [[CALCULATE]] does not use any measure. The following code is the one executed after the application of the calculation item:

```dax


CALCULATE (

    SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

)

```

If the expression inside [[CALCULATE]] contains multiple measure references, all of them are replaced with the calculation item definition. Any expression containing multiple measures might easily result in a complex behavior, likely to produce unexpected results. Therefore, if you need to filter a calculation item, we suggest as a best practice to do that on simple expressions that contain only one measure call.

For example, instead of writing the following code:

```dax


CR YTD :=

CALCULATE (

    DIVIDE ( 

        [Total Cost], 

        [Sales Amount]

    ),

    'Time Intelligence'[Time calc] = "YTD"

)

```

It is better to define a measure:

```dax


CostRatio :=

DIVIDE ( 

    [Total Cost], 

    [Sales Amount]

)

```

And, with the measure defined, replace the original code with the following:

```dax


CR YTD :=

CALCULATE (

    [Cost Ratio],

    'Time Intelligence'[Time calc] = "YTD"

)

```

There are no differences in terms of performance but, from the semantics point of view, it is much easier to apply calculation items to expressions with a single measure than to perform the same operation with more complex expressions.

When using client tools like Power BI, you never have to worry about these details. Indeed, these tools make sure that calculation items get applied the right way because they always invoke single measures as part of the query they execute. Nevertheless, as a DAX developer, you will end up using calculation items as filters in [[CALCULATE]] . When you do that, pay attention to the expression used in [[CALCULATE]] . If you want to stay on the safe side, use calculation items in [[CALCULATE]] to modify a single measure. Never apply calculation items to an expression.

## Conclusions

We suggest you learn calculation items by rewriting the expression manually, applying the calculation item, and writing down the complete code that will be executed. It is a mental exercise that proves very useful in understanding exactly what is happening inside the engine.

The best practice for using calculation items in DAX code is to apply them as filters in [[CALCULATE]] statements that only evaluate a single measure reference. If you are curious about the reasons for this best practice, make sure you do not miss the next article about [how calculation items are applied to measure references.](https://www.sqlbi.com/articles/understanding-the-application-of-calculation-items/)

[[SELECTEDMEASURE]]

Context transition

Returns the measure that is currently being evaluated.

`SELECTEDMEASURE ( )`

[[SELECTEDMEASUREFORMATSTRING]]

Returns format string for the measure that is currently being evaluated.

`SELECTEDMEASUREFORMATSTRING ( )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

## Articles in the Calculation Groups series

- [Introducing Calculation Groups](https://www.sqlbi.com/articles/introducing-calculation-groups/)
- *Understanding Calculation Groups*
- [Understanding the Application of Calculation Items](https://www.sqlbi.com/articles/understanding-the-application-of-calculation-items/)
- [Understanding Calculation Group Precedence](https://www.sqlbi.com/articles/understanding-calculation-group-precedence/)
- [Controlling Format Strings in Calculation Groups](https://www.sqlbi.com/articles/controlling-format-strings-in-calculation-groups/)
- [Avoiding Pitfalls in Calculation Groups Precedence](https://www.sqlbi.com/articles/avoiding-pitfalls-in-calculation-groups-precedence/)
- [Using calculation groups to selectively replace measures in DAX expressions](https://www.sqlbi.com/articles/using-calculation-groups-to-selectively-replace-measures-in-dax-expressions/)
- [Using calculation groups to switch between dates](https://www.sqlbi.com/articles/using-calculation-groups-to-switch-between-dates/)
- [Understanding the interactions between composite models and calculation groups](https://www.sqlbi.com/articles/understanding-the-interactions-between-composite-models-and-calculation-groups/)
