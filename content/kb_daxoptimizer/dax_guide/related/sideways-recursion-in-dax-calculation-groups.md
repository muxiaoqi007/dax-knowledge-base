---
title: "Sideways recursion in DAX calculation groups"
url: "https://www.sqlbi.com/articles/sideways-recursion-in-dax-calculation-groups"
published: "2025-08-12"
updated: 
source: "sqlbi.com"
---

# Sideways recursion in DAX calculation groups

> 发布：2025-08-12

DAX calculation items do not provide full recursion. However, a limited form of recursion is available, known as sideways recursion. We describe this complex topic through examples. Let us start by understanding what recursion is and why it is essential to discuss it. Recursion may occur when a calculation item refers to itself, resulting in an infinite loop within the [application of calculation items](https://www.sqlbi.com/articles/understanding-the-application-of-calculation-items/) (read the linked article in case you are not familiar with the concept of “application”, which is different from “execution”). Let us elaborate on this.

Consider a *Time Intelligence* calculation group with two calculation items defined as follows:

Calculation Items in Time Intelligence table

```dax


YTD =

CALCULATE (

    SELECTEDMEASURE (),

    DATESYTD ( 'Date'[Date] )

)



SPLY = 

CALCULATE (

    SELECTEDMEASURE (),

    SAMEPERIODLASTYEAR ( 'Date'[Date] )

)

```

The requirement is to add a third calculation item that computes the year-to-date in the previous year (**PYTD**). This can be obtained by mixing two time intelligence functions: [[DATESYTD]] and [[SAMEPERIODLASTYEAR]]. The following calculation item solves the scenario:

Calculation Item in Time Intelligence table

```dax


PYTD= 

CALCULATE (

    SELECTEDMEASURE (),

    DATESYTD ( SAMEPERIODLASTYEAR ( 'Date'[Date] ) )

)

```

Given the simplicity of the calculation, this solution is already optimal. Nevertheless, as a mind challenge, we can try to author the same code in a different way. Indeed, there already is a **YTD** calculation item that computes the year-to-date in place; therefore, one could think of using the calculation item instead of mixing time intelligence calculations within the same formula. Look at the following definition of the same **PYTD** calculation item:

Calculation Item in Time Intelligence table

```dax


PYTD= 

CALCULATE (

    SELECTEDMEASURE (),

    SAMEPERIODLASTYEAR ( 'Date'[Date] ),

    'Time Intelligence'[Time calc] = "YTD"

)

```

The calculation item achieves the same result as the previous definition, but using a different technique. [[SAMEPERIODLASTYEAR]] moves the filter context back to the previous year, while the year-to-date calculation is obtained by applying an existing calculation item in the *Time calc* calculation group: **YTD**. As previously noted, in this example, the code is less readable and needlessly more complex. That said, you can easily imagine that in a more complex scenario, the ability to invoke previously defi ned calculation items might come very handy—to avoid repeating the same code multiple times in your measures.

This is a powerful mechanism to define complex calculations. It comes with some level of complexity that needs to be well understood: recursion. As you have seen in the **PYTD** calculation item, it is possible to define a calculation item based on another calculation item from the same calculation group. In other words, inside a calculation group, certain items can be defined in terms of other items of the same calculation group. If the feature were available without any restriction, this would lead to extremely complex situations where calculation item A depends on B, which depends on C, which in turn can depend on A. The following fictitious example demonstrates the issue:

Calculation Items in Infinite table

```dax


Loop A =

CALCULATE (

    SELECTEDMEASURE (),

    Infinite[Loop] = "Loop B"

)



Loop B = 

CALCULATE (

    SELECTEDMEASURE (),

    Infinite[Loop] = "Loop A"

)

```

If used in an expression like in the following example, DAX would not be able to apply the calculation items, because A requires the application of B, which in turn requires A, and so on:

```dax


CALCULATE (

    [Sales Amount],

    Infinite[Loop] = "Loop A"

)

```

Some programming languages allow similar circular dependencies to be used in the definition of expressions (typically in functions), leading to recursive definitions. A recursive function definition is a definition where the function is defined in terms of itself. Recursion is extremely powerful, but it is also extremely complex for developers writing code and for the optimizer looking for the best execution path.

For these reasons, DAX does not allow the definition of recursive calculation items. In DAX, a developer can reference another calculation item of the same calculation group, but without referencing the same calculation item twice. In other words, it is possible to use [[CALCULATE]] to invoke a calculation item, but the calculation item invoked cannot directly or indirectly invoke the original calculation item. This feature is called *sideways recursion*. Its goal is not to implement full recursion; Instead, it aims at reusing complex calculation items without providing the full power (and complexity) of recursion.

Be mindful that recursion might also occur because a measure sets a filter on a calculation item, not only between calculation items. For example, consider the following definitions of measures (*Sales Amount*, *MA*, *MB*) and calculation items (**A** and **B**):

Measures in Sales table

```dax


Sales Amount = SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )



MA = CALCULATE ( [Sales Amount], Infinite[Loop] = "A" )



MB = CALCULATE ( [Sales Amount], Infinite[Loop] = "B" )

```

Calculation Items in Infinite table

```dax


Loop A = [MB]



Loop B = [MA]

```

The calculation items do not reference each other. Instead, they reference a measure that, in turn, references the calculation items, generating an infinite loop. We can see this happening by following the calculation item application step by step. Consider the following expression:

```dax


CALCULATE (

    [Sales Amount],

    Infinite[Loop] = "Loop A"

)

```

The application of calculation item A produces the following result:

```dax


CALCULATE (

    CALCULATE ( [MB] )

)

```

However, the *MB* measure internally references both *Sales Amount* and calculation item **B**; it corresponds to the following code:

```dax


CALCULATE (

    CALCULATE (

        CALCULATE (

            [Sales Amount],

            Infinite[Loop] = "B"

        )

    )

)

```

At this point, the application of calculation item B produces the following result:

```dax


CALCULATE (

    CALCULATE (

        CALCULATE (

            CALCULATE ( [MA] )

        )

    )

)

```

Again, the MA measure internally references Sales Amount and calculation item A, and corresponds to the following code:

```dax


CALCULATE (

    CALCULATE (

        CALCULATE (

            CALCULATE (

                CALCULATE (

                    [Sales Amount],

                    Infinite[Loop] = "A"

                )

            )

        )

    )

)

```

Now we are back to the initial expression, and we potentially enter into an infinite loop of calculation items applied to the expression, although the calculation items do not reference each other. Instead, they reference a measure that, in turn, references the calculation items. The engine is smart enough to detect that, in this case, an infinite loop is present. Therefore, DAX stops applying recursion and simply ignores the second application of the same calculation item. Therefore, the last filter on *Infinite[Loop]* is ignored, and the expression is evaluated without applying any other calculation items. If this were not the case, DAX should throw an error detecting the circular dependency. Thus, the recursion is limited to invoking other calculation items that have not already been applied; this is why it is called “sideways recursion”.

Sideways recursion can lead to very complex expressions that are hard to read and likely to produce unexpected results. Most of the complexity of calculation items with sideways recursion is seen when there are measures that internally apply calculation items with [[CALCULATE]]—all the while users change the calculation item through the user interface of the tool, like using a slicer in Power BI.

Our suggestion is to **limit the use of sideways recursion in your code as much as you can**, though this might mean repeating the same code in multiple places. Only in hidden calculation groups can you safely rely on sideways recursion, so that they can be managed by code but not by users. Keep in mind that Power BI users can define their own measures in a report, and, unaware of a complex topic like recursion, they might generate errors without properly understanding the reason.

In order to reuse the same snippet of DAX code in multiple expression, use DAX user-defined functions, because they do not require any form of recursion and are efficiently executed by the DAX engine.

[[DATESYTD]]

Context transition

Returns a set of dates in the year up to the last date visible in the filter context.

`DATESYTD ( <Dates> [, <YearEndDate>] )`

[[SAMEPERIODLASTYEAR]]

Context transition

Returns a set of dates in the current selection from the previous year.

`SAMEPERIODLASTYEAR ( <Dates> )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`
