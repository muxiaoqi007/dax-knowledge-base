---
title: "The definitive guide to ALLSELECTED"
url: "https://www.sqlbi.com/articles/the-definitive-guide-to-allselected"
published: "2020-08-17"
updated: 
source: "sqlbi.com"
---

# The definitive guide to ALLSELECTED

> 发布：2020-08-17

We have created several articles about [[ALLSELECTED]] and, unfortunately, we have never been completely clear about its behavior. The reason is very simple. We fell into one of the many traps of [[ALLSELECTED]]: we believed we understood its behavior, but we were just so close – not there yet.

After much time spent investigating its behavior and discussing this with the development team of Analysis Services, we finally managed to fully understand the [[ALLSELECTED]] function. This article describes its behavior. If you have read the first edition of the book, “The Definitive Guide to DAX”, consider this article an erratum of the book. Indeed, we are able to be a lot more precise and clear here, than when writing the book. We apologize for that.

Last disclaimer, before we start the article: this is not an introductory paper about how to use [[ALLSELECTED]]. We are not explaining when to use the function and what to use it for. Covering just the internals of [[ALLSELECTED]] resulted in writing around 20 pages; adding an introduction would be out of the scope of this paper. This paper is intended for readers wanting a deep explanation of the internals of [[ALLSELECTED]].

Let us start from the end, which is what [[ALLSELECTED]] does. It is expected that the reader has a hard time understanding the following statement at first glance. Indeed, the whole article aims to explain this single sentence:

> [[ALLSELECTED]] can either return a table or it can remove filters and restore a previous filter context. In both cases, it does so by accessing and using the last shadow filter context left by an iterator on the stack of filter contexts.

## [[ALLSELECTED]] as a table function or as a [[CALCULATE]] modifier

Before going further, we need to answer an important question: is [[ALLSELECTED]] a table function or does it behave as a [[CALCULATE]] modifier, the way [[KEEPFILTERS]] and [[USERELATIONSHIP]] do? It shows both behaviors, depending on the number of parameters and on the context where it is used. In fact, [[ALLSELECTED]] can be used with three different parameters: a table, a column, or no parameter at all, as in the following few examples:

```dax


AllSelectedColumn := 

CALCULATE ( 

    [SalesAmount], 

    ALLSELECTED ( Customer[Occupation] ) 

)



AllSelectedTable := 

CALCULATE ( 

    [SalesAmount], 

    ALLSELECTED ( Customer ) 

)



AllSelectedAll := 

CALCULATE ( 

    [SalesAmount], 

    ALLSELECTED () 

)

```

The following figure shows the result of these three measures when used in a matrix containing product class, customer gender and customer occupation:

[![](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-00.png)](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-00.png)

> **IMPORTANT NOTE**: A matrix in Power BI and a pivot table in Excel evaluate the measure of each cell using the equivalent of a DAX iterator that evaluates the measure for each combination of the attributes used in rows and columns of the user interface. This is important to consider for the remaining part of this article.

These examples shows [[ALLSELECTED]] as a [[CALCULATE]] modifier. Nevertheless, [[ALLSELECTED]] can be used also as a table function, like in the following code:

```dax


AllSelectedCustomerSales := 

SUMX (

    ALLSELECTED ( Customer ), 

    [SalesAmount]

)

```

When used as a table function, [[ALLSELECTED]] returns a subset of the table or a subset of the values of the column following the rules explained below. On the other hand, when used with no parameters, as in the following code, [[ALLSELECTED]] can only be used as a [[CALCULATE]] modifier:

```dax


AllSelectedSales := 

CALCULATE ( 

    [SalesAmount], 

    ALLSELECTED () 

)

```

For now, the focus is on [[ALLSELECTED]] when used with a table or a column – later we will cover [[ALLSELECTED]] with no parameters, acting only as a [[CALCULATE]] modifier. Before performing a deeper analysis of [[ALLSELECTED]], we need to introduce shadow filter contexts, as they are of paramount importance in the description of [[ALLSELECTED]] .

## Introducing shadow filter contexts

Shadow filter contexts are a special kind of filter context created by iterators and, in their initial state, they are inactive. An inactive filter context simply stays there dormant, and it does not affect code behavior in any way. Still, it is there, and it is important for the sake of this article because [[ALLSELECTED]] activates shadow filter contexts as part of its execution.  
We consider the following measure:

```dax


SalesAmount := 

SUMX ( 

    Sales, 

    Sales[Quantity] * Sales[Net Price] 

)

```

Being an iterator, [[SUMX]] generates a shadow filter context that contains the Sales table. It does not contain the whole Sales table. It only contains the rows that are visible in the current filter context, as usual. Being a shadow filter context, it is inactive. Thus, the filter context does not affect calculations and this is the reason why shadow filter contexts are not very talked about. To demonstrate this, we look at the following code which behaves as expected. It results in the total sales quantity multiplied by the number of colors. The lack of a context transition calling [[SUM]] is intentional:

```dax


WrongSalesAmount := 

SUMX ( 

    VALUES ( Product[Color] ), 

    SUM ( Sales[Quantity] ) 

)

```

During the iteration, the row context on Color is not transformed into a filter context, as there is no [[CALCULATE]] performing a context transition. As explained here, there is indeed a filter context: the “shadow” filter context. The shadow filter context includes the list of colors that are active in the current filter context when the iterator starts. Indeed, the shadow filter context is not active during the iteration, unless it is turned on by a function – namely [[ALLSELECTED]]. In other words, it is safe to ignore the existence of shadow filter contexts, when not using [[ALLSELECTED]]. On the other hand, when the user decides to take advantage of [[ALLSELECTED]], then shadow filter contexts are of paramount importance.

A careful reader might notice that if the shadow filter context had been active, it would not have changed the result. In fact, the shadow filter context containing all the colors, the result would be the very same as with the original filter context. But, with a more elaborate expression like the one below, complexity starts to increase:

```dax


AnotherSalesAmount := 

SUMX ( 

    CALCULATETABLE ( 

        VALUES ( Product[Color] ),

        Product[Color] = "Red"

    ),

    SUM ( Sales[Quantity] )

)

```

In this case, the shadow filter context contains a selection of colors – namely, only red. However inside the iteration, [[SUM]] ( Sales[Quantity] ) still computes the sum of all the sales. If the shadow filter context were active, then the engine would only sum red sales.  
In this article, we refer to normal filters (that is, non-shadow filters) as explicit filters, with the sole purpose of differentiating between explicit filters and shadow filters when needed.  
Now that we know about shadow filter contexts, we can start analyzing the behavior of [[ALLSELECTED]] on sample data.

## Sample data

We use a table with nine rows and only three columns: Product, Brand, and Color:

[![](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-01.png)](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-01.png)

On this table, we use an outer [[CALCULATETABLE]] to filter some colors and brands. We also apply an unusual measure, ListProductNames. ListProductNames uses [[CONCATENATEX]] to return the list of product names visible in the current filter context. The purpose of the measure is to let us analyze both the result of [[ALLSELECTED]], when used as a table function, and the result of a table, when [[ALLSELECTED]] is used as a [[CALCULATE]] modifier.

```dax


DEFINE

    MEASURE Product[ListProductNames] =

        CONCATENATEX ( 

            VALUES ( 'Product'[Product] ), 

            Product[Product], 

            ", " 

        )

EVALUATE

CALCULATETABLE (

    ROW ( "Products", [ListProductNames] ),

    Product[Color] IN { "Blue", "Green" },

    Product[Brand] IN { "Contoso", "Fabrikam" }

)

```

The result of this query is: Helmet, Shoes, Keyboard, Piano. The following figure displays the two filters generated for Color and Brand, and the resulting filter context:

[![](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-02.png)](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-02.png)

So far, there is nothing new. In the next sections, we are going to use the same code snippet with [[ALLSELECTED]] to analyze its behavior.

## [[ALLSELECTED]] with a column

[[ALLSELECTED]] ( Table[Column] ) returns the values of the column that were visible in the last shadow filter context that actually filtered the column. In the following code, we use [[ALLSELECTED]] on Product[Product] to retrieve the list of all product names that were visible in the last shadow filter context. Before moving further with the reading, one can try to guess what the result will be.

```dax


EVALUATE

CALCULATETABLE (

    ROW (

        "Products", CONCATENATEX ( 

            ALLSELECTED ( 'Product'[Product] ), 

            Product[Product], 

            ", " 

        )

    ),

    Product[Color] IN { "Blue", "Green" },

    Product[Brand] IN { "Contoso", "Fabrikam" }

)

```

A first guess would be to follow the filters as in the previous picture. The answer would then have been that the result is the same as before: only the four products that are blue, green and of Contoso and Fabrikam – corresponding to Helmet, Shoes, Keyboard, Piano, as in the previous example. Actually, the result is very different – it returns all the products:

> Bike, Helmet, Shoes, Robot, Shirt, Rollerblades, Motorbike, Keyboard, Piano

Indeed, [[ALLSELECTED]] returns the values of a column as filtered by the last shadow filter context. The reader might have noticed that there is no active iteration when we call [[ALLSELECTED]]. Thus, there are no shadow filter contexts to activate. As a result, all the product names are returned because neither Color nor Brand filter the Product[Product] column – although it is cross-filtered.

This is the first lesson to learn here: [[ALLSELECTED]] does not take into account the filter context. Its main purpose is that of retrieving a previously set shadow filter context. [[ALLSELECTED]] is very different from [[VALUES]] or [[DISTINCT]]. [[VALUES]] and [[DISTINCT]] always take into account the filter context, whereas [[ALLSELECTED]] does not. [[ALLSELECTED]] works on a column and checks whether that column is filtered by a shadow filter context, ignoring any cross-filter.

Things may be more confusing for readers used to writing the previous code this way:

```dax


EVALUATE

CALCULATETABLE (

    ROW (

        "Products", CALCULATE ( 

            CONCATENATEX ( 

                VALUES ( 'Product'[Product] ), 

                Product[Product], 

                ", " 

            ),

            ALLSELECTED ( Product[Product] )

        )

    ),

    Product[Color] IN { "Blue", "Green" },

    Product[Brand] IN { "Contoso", "Fabrikam" }

)

```

This time the result will be as expected, namely: Helmet, Shoes, Keyboard, Piano. When we read it, we tend to think that this is because [[ALLSELECTED]] ( Product[Product] ) applied a filter on the Product[Product] column. But this is not what is happening. [[ALLSELECTED]], in this case, does not apply any kind of filter to the Product[Product] column, because there are no shadow filter contexts. When called, it is [[VALUES]] that analyzes the products visible in the current context by observing the filters on Color and Brand. [[ALLSELECTED]] did not contribute to the filtering in any way. In other words, it is not [[ALLSELECTED]] that filters Product[Product], but the effect of cross-filtering from Color and Brand, made explicit by [[VALUES]].

So far, we have seen that [[ALLSELECTED]] did not filter a column in any way. It is now time to generate a shadow filter context and to ask [[ALLSELECTED]] to activate it for us. To do so, we need an iteration – we replace [[ROW]] with [[ADDCOLUMNS]], iterating on Brand as in the following code:

```dax


EVALUATE

CALCULATETABLE (

    ADDCOLUMNS (

        VALUES ( 'Product'[Brand] ),

        "Brands", 

        CONCATENATEX ( 

            ALLSELECTED ( 'Product'[Brand] ), 

            Product[Brand], 

            ", " 

        )

    ),

    Product[Color] IN { "Blue", "Green" },

    Product[Brand] IN { "Contoso", "Fabrikam" }

)

```

The result of this query is, as expected, two lines returning Contoso, Fabrikam for each brand.

[![](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-03.png)](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-03.png)

At first sight, it seems as if [[ALLSELECTED]] has restored the filter coming from the outer [[CALCULATETABLE]]. However, that is not the case although it returns the same result. What happens is that [[ADDCOLUMNS]] – which is an iterator – generates a shadow filter context containing the iterated table. The iterated table is the result of [[VALUES]](Product[Brand]) that – being evaluated in a filter context that contains only Contoso and Fabrikam – returns these two brands.

[[ALLSELECTED]] returned the brands visible in the last shadow filter context, producing the expected result. How can it be ascertained that this is indeed the behavior of [[ALLSELECTED]]? One can just try to replace [[VALUES]] with [[ALL]], so that the iteration of [[ADDCOLUMNS]] does no longer happen on two brands, but on all brands instead:

```dax


EVALUATE

CALCULATETABLE (

    ADDCOLUMNS (

        ALL ( 'Product'[Brand] ),

        "Brands", CONCATENATEX ( 

            ALLSELECTED ( 'Product'[Brand] ), 

            Product[Brand], 

            ", " 

        )

    ),

    Product[Color] IN { "Blue", "Green" },

    Product[Brand] IN { "Contoso", "Fabrikam" }

)

```

This time, the result will be a table with three rows, each of which shows all the brands:

[![](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-04.png)](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-04.png)

The reason is that [[ADDCOLUMNS]] now iterates [[ALL]](Product[Brand]). Thus, the shadow filter context contains all the values for Product[Brand]. When activated, the shadow filter context shows all values despite the outer [[CALCULATETABLE]] filtering only two out of three brands.

We now make things more complex by using two iterators on the same column. For syntax purposes, we will need to rename the columns using [[SELECTCOLUMNS]] to avoid name conflicts. Here is a more elaborate example:

```dax


EVALUATE

CALCULATETABLE (

    GENERATE (

        SELECTCOLUMNS ( 

            ALL ( 'Product'[Brand] ), 

            "Outer Brand", Product[Brand] 

        ),

        GENERATE (

            SELECTCOLUMNS ( 

                VALUES ( 'Product'[Brand] ), 

                "Inner Brand", Product[Brand] 

            ),

            ROW (

                "Brands", CONCATENATEX ( 

                    ALLSELECTED ( 'Product'[Brand] ), 

                    Product[Brand], 

                    ", " 

                )

            )

        )

    ),

    Product[Color] IN { "Blue", "Green" },

    Product[Brand] IN { "Contoso", "Fabrikam" }

)

```

We have two nested iterations on the same Product[Brand] column, and in the innermost part we use [[ALLSELECTED]] as a table function. The result is the list of brands iterated in the nearest shadow filter context. Since the nearest shadow filter context is generated by the iteration that scans [[VALUES]] ( Product[Brand] ) , and since [[VALUES]] follows the filter context created by the outermost [[CALCULATETABLE]], the result includes only two brands – the inner brands:

[![](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-05.png)](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-05.png)

Swapping [[VALUES]] and [[ALL]] in the inner and outer brands would result in a different result. Indeed, the inner iteration would then scan [[ALL]] and [[ALLSELECTED]] would return all the values:

```dax


EVALUATE

CALCULATETABLE (

    GENERATE (

        SELECTCOLUMNS ( 

            VALUES ( 'Product'[Brand] ), 

            "Outer Brand", Product[Brand] 

        ),

        GENERATE (

            SELECTCOLUMNS ( 

                ALL ( 'Product'[Brand] ), 

                "Inner Brand", Product[Brand] 

            ),

            ROW (

                "Brands", CONCATENATEX ( 

                    ALLSELECTED ( 'Product'[Brand] ), 

                    Product[Brand], 

                    ", " 

                )

            )

        )

    ),

    Product[Color] IN { "Blue", "Green" },

    Product[Brand] IN { "Contoso", "Fabrikam" }

)

```

In fact, this is the result:

[![](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-06.png)](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-06.png)

This concludes the discussion on the iterated Brand column. Moving on to the product name, if we query [[ALLSELECTED]] ( Product[Product] ) inside two iterations on Brand, what result should we expect? This is easily resolved using the proper rules: [[ALLSELECTED]] returns all the product names visible in the last shadow filter context. Here is an opportunity to guess the answer while looking at the code:

```dax


EVALUATE

CALCULATETABLE (

    GENERATE (

        SELECTCOLUMNS ( 

            VALUES ( 'Product'[Brand] ), 

            "Outer Brand", Product[Brand] 

        ),

        GENERATE (

            SELECTCOLUMNS ( 

                ALL ( 'Product'[Brand] ), 

                "Inner Brand", Product[Brand] 

            ),

            ROW (

                "Products", CONCATENATEX ( 

                    ALLSELECTED ( 'Product'[Product] ), 

                    Product[Product], 

                    ", " 

                )

            )

        )

    ),

    Product[Color] IN { "Blue", "Green" },

    Product[Brand] IN { "Contoso", "Fabrikam" }

)

```

The solution is rather straightforward: because there is no shadow filter context filtering the product name, [[ALLSELECTED]] simply returns all the product names:

[![](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-07.png)](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-07.png)

A good question to ponder at this point, is whether one can go back to the second last shadow filter context, as is possible with [[EARLIER]] with row contexts. The answer is no, if only relying on [[ALLSELECTED]]. With that said, DAX does not only offer [[ALLSELECTED]], it offers a much more powerful mechanism to play with outer contexts, be they explicit or shadow contexts: variables. If, inside the inner iteration, one wants to access the outer context of Brand, one only needs save the [[ALLSELECTED]] values of Product[Brand] in a variable before the innermost shadow filter context kicks in, as in the following example:

```dax


EVALUATE

CALCULATETABLE (

    GENERATE (

        SELECTCOLUMNS ( 

            VALUES ( 'Product'[Brand] ), 

            "Outer Brand", Product[Brand] 

        ),

        VAR OuterProducts =

            ALLSELECTED ( 'Product'[Brand] )

        RETURN

            GENERATE (

                SELECTCOLUMNS ( 

                    ALL ( 'Product'[Brand] ), 

                    "Inner Brand", Product[Brand] 

                ),

                ROW ( 

                    "Brands", CONCATENATEX ( 

                        OuterProducts, 

                        'Product'[Brand], 

                        ", " 

                    ) 

                )

            )

    ),

    Product[Color] IN { "Blue", "Green" },

    Product[Brand] IN { "Contoso", "Fabrikam" }

)

```

The result, which we suggest the readers also test by themselves, makes visible only Contoso and Fabrikam, which are the brands in the outer filter context.

[![](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-08.png)](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-08.png)

As a reminder, using variables makes the code much easier to author and debug later: one must get used to them, as they play a very important role in complex DAX code.  
So far, we have deeply analyzed the semantics of [[ALLSELECTED]] when used as a table function with a single column. The next step is to add some complexity and use it with a full table as a parameter.

## [[ALLSELECTED]] with a table

Things start to get a little more intricate with [[ALLSELECTED]] ( Product ). In fact, a table contains multiple columns. On each column, there might be an explicit filter – set with [[CALCULATE]] – or a shadow filter set by an iterator. Moreover, multiple filters – either explicit or shadow filters – can be nested on each column, making things even more complex. Let us start studying [[ALLSELECTED]] with a full table.

The first noticeable difference between using [[ALLSELECTED]] with a table and using it with a column comes with the simplest of our test queries:

```dax


EVALUATE

CALCULATETABLE (

    ROW (

        "Products", CONCATENATEX ( 

            ALLSELECTED ( 'Product' ), 

            Product[Product], 

            ", " 

        )

    ),

    Product[Color] IN { "Blue", "Green" },

    Product[Brand] IN { "Contoso", "Fabrikam" }

)

```

At the beginning of this article, we have stated that [[ALLSELECTED]] ignores the explicit filters on a column and it uses only shadow filter contexts. This is true if used with a column parameter, but it yields false if used with a table parameter. In fact, the result of the previous query is:

> Helmet, Shoes, Keyboard, Piano.

In other words, [[ALLSELECTED]] with a table used the explicit column filters created by [[CALCULATETABLE]] and returned only the products that are blue or green and Contoso or Fabrikam. The reader will note that in this first query, there are no shadow filter contexts. There are only explicit filter contexts.

What happens if we introduce an iteration on one of the columns? This query shows the behavior to be expected:

```dax


EVALUATE

CALCULATETABLE (

    ADDCOLUMNS (

        ALL ( 'Product'[Color] ),

        "Products", CONCATENATEX ( 

            ALLSELECTED ( 'Product' ), 

            Product[Product], 

            ", " 

        )

    ),

    Product[Color] IN { "Blue", "Green" },

    Product[Brand] IN { "Contoso", "Fabrikam" }

)

```

Since [[ADDCOLUMNS]] iterates on all the product colors, now there is a shadow filter context containing all the colors. This shadow filter context is activated by [[ALLSELECTED]] and it replaces the previous filter on blue and green. On the other hand, the Brand column – which is not filtered by the shadow filter context – still maintains the explicit filter on Contoso and Fabrikam. The result is the following, showing all the products that are Contoso or Fabrikam, in any color. In other words, the filter on Color is replaced, the filter on Brand is not. We suggest the reader look at the original data, to fully make sense of this complex result.

[![](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-09.png)](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-09.png)

If both columns have a shadow filter context – due to a double iteration like in the following code – then the two shadow filters completely replace the explicit filters originated by the outermost [[CALCULATETABLE]], as in the following code:

```dax


EVALUATE

CALCULATETABLE (

    GENERATE (

        ALL ( Product[Brand] ),

        ADDCOLUMNS (

            ALL ( 'Product'[Color] ),

            "Products", CONCATENATEX ( 

                ALLSELECTED ( 'Product' ), 

                Product[Product], 

                ", " 

            )

        )

    ),

    Product[Color] IN { "Blue", "Green" },

    Product[Brand] IN { "Contoso", "Fabrikam" }

)

```

The result in the Products column of this last query is the list of all the products, because both Color and Brand explicit filters have been replaced by the corresponding shadow filters. Thus, [[ALLSELECTED]] restores the last shadow filter context on all the columns that have a shadow filter context, maintaining the explicit filter, if any, on the other columns.

[![](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-10.png)](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-10.png)

The next query is a tough one: there is an outer shadow filter context containing all the brands and an inner shadow filter context containing only two brands and two colors. Because [[ALLSELECTED]] will activate the last shadow filter context on each column, it will stop on the innermost filter context. Indeed, it already filters both Brand and Color. Thus, in this case the outer shadow filter context is not activated:

```dax


EVALUATE

CALCULATETABLE (

    GENERATE (

        SELECTCOLUMNS ( 

            ALL ( Product[Brand] ), 

            "Outer Brand", Product[Brand] 

        ),

        ADDCOLUMNS (

            CROSSJOIN (

                VALUES ( Product[Color] ),

                SELECTCOLUMNS ( 

                    VALUES ( Product[Brand] ), 

                    "Inner Brand", Product[Brand] 

                )

            ),

            "Products", CONCATENATEX ( 

                ALLSELECTED ( 'Product' ), 

                Product[Product], 

                ", " 

            )

        )

    ),

    Product[Color] IN { "Blue", "Green" },

    Product[Brand] IN { "Contoso", "Fabrikam" }

)

```

The content produced in the Products column is the same for all the rows of the result:

> Helmet, Shoes, Keyboard and Piano

The outer [[ALL]] on Brand does not activate, being overridden by the innermost shadow filter context. The complete result is as follows:

[![](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-11.png)](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-11.png)

## First review

Let’s consider a summary of our findings so far.

- Iterators create shadow filter contexts.
- [[CALCULATE]] creates explicit filter contexts.
- [[ALLSELECTED]] is a table function that returns a different result when used with a table or with a column.
- With a column, [[ALLSELECTED]] returns the values of the column considering only the last shadow filter, if any. [[ALLSELECTED]] returns all column values if no shadow filter context exists.
- With a table, [[ALLSELECTED]] returns a table containing all the rows remaining after applying the last shadow filter on any column that has a shadow filter; or, [[ALLSELECTED]] returns the last explicit filter context if no shadow filter context is available.

At this point, we bring the reader’s attention to the fact that [[ALLSELECTED]] is very often used as a [[CALCULATE]] modifier rather than as a table function. We have already seen that these two measures below return a different result:

```dax


AllSelectedWithCALCULATE := 

CALCULATE ( 

    CONCATENATEX ( 

        VALUES ( 'Product'[Product] ), 

        Product[Product], 

        ", " 

    ),

    ALLSELECTED ( Product[Product] )

)



AllSelectedAsTableFunction := 

CALCULATE ( 

    CONCATENATEX ( 

        ALLSELECTED ( 'Product'[Product] ), 

        Product[Product], 

        ", " 

    )

)

```

In fact, in the measures shown above, the external filter context is injected by [[VALUES]], not by [[ALLSELECTED]], as shown earlier. Moreover, [[ALLSELECTED]] operates in a similar way to [[ALL]], which removes corresponding filters from the filter context when used as a [[CALCULATE]] modifier.  
Nevertheless, the following scenarios have yet to be analyzed:

- The usage of [[ALLSELECTED]] with no parameter, when [[ALLSELECTED]] is no longer a table function, but only a [[CALCULATE]] modifier.
- Interaction between [[ALLSELECTED]] and context transition.

## [[ALLSELECTED]] with no parameters

In the total absence of parameters, [[ALLSELECTED]] is obviously not a table function. Instead, [[ALLSELECTED]] can be used only as a filter parameter of [[CALCULATE]]. Intuitively, it performs [[ALLSELECTED]] on all the columns that are referenced by some shadow filter context. In practice, restoring the last shadow filter context for each column.

This deep into the article, it is no longer necessary to proceed step-by-step. We shall present a single example allowing the reader to draw conclusions. We define two measures:

```dax


AllSel := 

CALCULATE ( 

    SUM ( Sales[Quantity] ), 

    ALLSELECTED () 

)



AllSelSumX :=

SUMX (

    VALUES ( 'Product'[Brand] ),

    SUMX (

        VALUES ( 'Product'[Color] ),

        CALCULATE ( 

            SUM ( Sales[Quantity] ), 

            ALLSELECTED () 

        )

    )

)

```

Then, we project them in a Power BI report:

[![](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-12.png)](https://cdn.sqlbi.com/wp-content/uploads/AllSelected-Internals-12.png)

In the report, only Contoso and Fabrikam are selected, for a total of 6 products. The value of AllSel looks correct, since it reports, for each row, the total of Quantity in the matrix. The confusing results are in AllSelSumX: the only number that looks correct is the grand total (300×6=1800), whereas all the other numbers look wrong.

Beware: the numbers look wrong to a user with a simple understanding of [[ALLSELECTED]]. Indeed, they are perfectly correct once it is clear how [[ALLSELECTED]] works. In fact, the explanation is straightforward if we focus on the value of 30 (Contoso/Blue). We start with a filter context containing only one product, one brand, one color. Both nested iterations ([[SUMX]] on Brand and [[SUMX]] on Color) iterate exactly one row, which means that both [[SUMX]] functions generate a shadow filter context containing only one value. [[ALLSELECTED]] revives these shadow filter contexts so that [[SUM]] is executed in a filter context containing exactly one row. Hence, the value computed for each row is the value of the only product visible in the filter context of the given row, because [[ALLSELECTED]] revived it by activating the two shadow filter contexts. Following the same path, all the values for AllSelSumX start to make more sense because now it is clear how [[ALLSELECTED]] works, restoring shadow filter contexts. We strongly suggest the reader take a minute to make sense, at least, of the Contoso row before reading the hints below:

> 180 equals 60 multiplied by three; one iteration over the brand; three iterations over the colors; and, the innermost shadow filter context on color contains the three colors.

This is not to say that the value shown for AllSelSumX makes any kind of sense. It is very likely that end users along with the reader, consider this number to be just wrong. Here, the goal is not that of finding a way to compute numbers properly. Instead we focus on understanding exactly how [[ALLSELECTED]] works, so that we rely on it and use it only when it produces what we need.

## [[ALLSELECTED]] and context transition

In previous articles ([Understanding ALLSELECTED](https://www.sqlbi.com/articles/understanding-allselected/)) and, unfortunately, in the first edition of the book, “The Definitive Guide to DAX”, we described [[ALLSELECTED]] by saying that [[ALLSELECTED]] removes the last filter context generated by a context transition. Unfortunately, this was the wrong description. This calls for further explaining.

Does [[ALLSELECTED]] interact with context transitions in any way? We know that [[CALCULATE]] generates a filter context equivalent to any existing row context as part of its filter context creation. We also know that this filter context generated by context transition has a lower precedence against any explicit filter context. Using [[ALLSELECTED]] as a [[CALCULATE]] filter argument means transforming a shadow filter context into an explicit filter context. This way, it gains precedence against the filter context generated by context transition.  
Thus, it looks like [[ALLSELECTED]] removes the last filter context generated by context transition. No such removal happens. The effect of removing the last filter context generated by context transition is only a side effect of the transformation of a shadow filter context into an explicit filter context. Once the semantics of [[ALLSELECTED]] are clear in terms of shadow filter contexts, explaining its behavior becomes much easier.

## Conclusion

As described here, the behavior of [[ALLSELECTED]] is easier to understand when one is more familiar with shadow filter contexts. With that said, [[ALLSELECTED]] is a very complex function because of the presence of shadow filter contexts, and because their interaction with the explicit filter contexts makes it very hard to elaborate on the results.

What we typically teach users during our trainings is to use [[ALLSELECTED]] to retrieve the query context – that is, the context under which a pivot table or a report is executed – if and only if no iteration is happening. If there are any number of iterations, users are encouraged to avoid using [[ALLSELECTED]] because the results are almost unpredictable. In reality, results are very complex to understand. This article presents all the tools needed to understand the behavior of [[ALLSELECTED]]. That said, we would not want to have to perform all these complex reasoning steps every time we need to debug a measure. Thus, the suggestion still stands: [[ALLSELECTED]] should not be used inside an iteration, unless the user has established a very clear understanding of what they are doing and there is a strong need for it. In most scenarios, variables make it possible to avoid using [[ALLSELECTED]] inside an iteration. Variables are strongly suggested to that effect.

[[ALLSELECTED]]

CALCULATE modifier

Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied inside the query, but keeping filters that come from outside.

`ALLSELECTED ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[KEEPFILTERS]]

CALCULATE modifier

Changes the CALCULATE and CALCULATETABLE function filtering semantics.

`KEEPFILTERS ( <Expression> )`

[[USERELATIONSHIP]]

CALCULATE modifier

Specifies an existing relationship to be used in the evaluation of a DAX expression. The relationship is defined by naming, as arguments, the two columns that serve as endpoints.

`USERELATIONSHIP ( <ColumnName1>, <ColumnName2> )`

[[SUMX]]

Returns the sum of an expression evaluated for each row in a table.

`SUMX ( <Table>, <Expression> )`

[[SUM]]

Adds all the numbers in a column.

`SUM ( <ColumnName> )`

[[CALCULATETABLE]]

Context transition

Evaluates a table expression in a context modified by filters.

`CALCULATETABLE ( <Table> [, <Filter> [, <Filter> [, … ] ] ] )`

[[CONCATENATEX]]

Evaluates expression for each row on the table, then return the concatenation of those values in a single string result, seperated by the specified delimiter.

`CONCATENATEX ( <Table>, <Expression> [, <Delimiter>] [, <OrderBy_Expression> [, [<Order>] [, <OrderBy_Expression> [, [<Order>] [, … ] ] ] ] ] )`

[[VALUES]]

When a column name is given, returns a single-column table of unique values. When a table name is given, returns a table with the same columns and all the rows of the table (including duplicates) with the [additional blank row](https://www.sqlbi.com/articles/blank-row-in-dax/) caused by an invalid relationship if present.

`VALUES ( <TableNameOrColumnName> )`

[[DISTINCT]]

Returns a one column table that contains the distinct (unique) values in a column, for a column argument. Or multiple columns with distinct (unique) combination of values, for a table expression argument.

`DISTINCT ( <ColumnNameOrTableExpr> )`

[[ROW]]

Returns a single row table with new columns specified by the DAX expressions.

`ROW ( <Name>, <Expression> [, <Name>, <Expression> [, … ] ] )`

[[ADDCOLUMNS]]

Returns a table with new columns specified by the DAX expressions.

`ADDCOLUMNS ( <Table>, <Name>, <Expression> [, <Name>, <Expression> [, … ] ] )`

[[ALL]]

CALCULATE modifier

Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied.

`ALL ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[SELECTCOLUMNS]]

Returns a table with selected columns from the table and new columns specified by the DAX expressions.

`SELECTCOLUMNS ( <Table> [[, <Name>], <Expression> [[, <Name>], <Expression> [, … ] ] ] )`

[[EARLIER]]

Returns the value in the column prior to the specified number of table scans (default is 1).

`EARLIER ( <ColumnName> [, <Number>] )`
