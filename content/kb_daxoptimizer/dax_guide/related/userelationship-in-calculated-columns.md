---
title: "USERELATIONSHIP in calculated columns"
url: "https://www.sqlbi.com/articles/userelationship-in-calculated-columns"
published: "2021-03-16"
updated: 
source: "sqlbi.com"
---

# USERELATIONSHIP in calculated columns

> 发布：2021-03-16

The conclusion of this article is very short: do not use [[USERELATIONSHIP]] inside a calculated column to try to change the active relationship. If you need to retrieve a column from another table, use [[LOOKUPVALUE]] instead and do not rely on relationships.

This article aims at covering the reasons for this limitation, and as such it is both long and extremely complicated. You need a deep understanding of evaluation contexts, context transition, the precise semantics of [[CALCULATE]] as well as expanded tables. These are prerequisites to reading this article. This article is extremely dense and it requires attention to be understood well.

With that said, for the bravest among our loyal readers we provide a complete explanation of the many challenges that come with activating a relationship from inside a calculated column. It is indeed a great benchmark to test your understanding of the most intricate aspects of DAX!

First, a brief recap of what [[USERELATIONSHIP]] does. When you have multiple relationships between tables, [[USERELATIONSHIP]] lets you temporarily activate an inactive relationship, while automatically deactivating any conflicting relationship. For this article, we are using a basic data model with only two tables.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-9.png)

Moreover, in order to further simplify the description, the *Sales* table contains only few rows, whereas *Date* is a calculated table created with [[CALENDARAUTO]]. You see the content of *Sales* below.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-7.png)

By using [[USERELATIONSHIP]] you can author **two measures**: one that computes the sales amount, and another measure to compute the delivered amount:

```dax


Sales Amount := 

SUM ( Sales[Amount] )



Delivery Amount :=

CALCULATE (

    SUM ( Sales[Amount] ),

    USERELATIONSHIP ( Sales[DeliveryDate], 'Date'[Date] )

)

```

As you can see in the following report, Claire has placed orders in 2020, but she received the items only in 2021.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-4.png)

In the *Delivery Amount* measure, we use [[USERELATIONSHIP]] to temporarily activate the relationship between *Sales[DeliveryDate]* and *Date[Date]*. The activated relationship is enabled for the duration of [[CALCULATE]] only. Outside of [[CALCULATE]], the default relationship – the one with *Sales[OrderDate]* – is still the active relationship.

It is important to note that [[USERELATIONSHIP]] is a [[CALCULATE]] modifier. It needs to be used as part of a [[CALCULATE]] statement. Even though this is not an issue when authoring measures, the need to use [[CALCULATE]] brings with it some important side-effects when used in a calculated column or from inside an iteration. Indeed, the main problem with mixing USERELATIONSHIPS and row context is the context transition.

Beware that the code we show in the remaining part of the article is for educational purposes only. It does not reflect best practices and it is not intended to be used in a real-world scenario. Our goal is to show the semantics of the DAX language through examples that work on small datasets. On larger datasets, you need to fine-tune the code, something we are not worried about here. Indeed, we will spend time computing the year of the delivery date, which is as simple as [[YEAR]] ( *Sales[DeliveryDate]* ). That said, this article is all about understanding the theory; therefore, educational examples are welcome, although not optimal.

Finally, in the article we describe several incorrect formulas before getting to the correct one. The reason is that there are a lot of small details which are dangerously subtle, yet crucial. By showing different versions of the code we are able to highlight each single detail – thus avoiding the complexity of a single expression that would be very hard to understand in depth, despite the fact that it works!

When working with the row context, we are used to performing lookups on related tables through the [[RELATED]] function. [[RELATED]] uses the active relationship to retrieve a column from a related table. In our example, a **calculated column** that computes the year of the order would be as simple as this:

```dax


OrderYear = RELATED ( 'Date'[Year] )

```

Because the active relationship is the one between *Sales[OrderDate]* and *Date[Date]*, the result is the year of the order for each row. Even though it is extremely simple to retrieve the value of a related column by using the default relationships, it is much more complex to perform the same operation using an inactive relationship. The reason is that you need [[CALCULATE]] to activate the [[USERELATIONSHIP]] modifier; [[CALCULATE]] removes the current row context, transforming it into a filter context due to context transition.

Therefore, a simple syntax like the following one just does not work. All you get is an error message:

```dax


DeliveryYear = 

CALCULATE ( 

    RELATED ( 'Date'[Year] ) ),

    USERELATIONSHIP ( Sales[DeliveryDate], 'Date'[Date] )

)

```

The problem is that inside [[CALCULATE]], the row context is no longer available. Because there is no row context, you cannot use [[RELATED]] nor any other function that relies on the row context.

If the row context is not an option because of [[CALCULATE]], we can leverage the filter context. Let us start by using the filter context to compute the order year. Because [[CALCULATE]] performs a context transition, you can compute the *OrderYear* column by leveraging only the context transition executed by [[CALCULATE]] as part of its semantics:

```dax


OrderYear = CALCULATE ( SELECTEDVALUE ( 'Date'[Year] ) )

```

[[CALCULATE]] performs a context transition; therefore, the current row context is turned into a filter context that filters all the columns of the expanded *Sales* table. If you are not familiar with expanded tables, please refresh your memory by reading this article: [Expanded tables in DAX](https://www.sqlbi.com/articles/expanded-tables-in-dax/). Because the expanded *Sales* table includes *Date*, the filter context reaches *Date* and filters the year of the order. Hence, [[SELECTEDVALUE]] computes the correct sales year.

If we want to compute the delivery year, a similar expression using [[USERELATIONSHIP]] to change the active relationship does not work:

```dax


DeliveryYear = 

CALCULATE ( 

    SELECTEDVALUE ( 'Date'[Year] ),

    USERELATIONSHIP ( Sales[DeliveryDate], 'Date'[Date] )

)

```

This version of *DeliveryYear* returns the very same value as *OrderYear*, as if [[USERELATIONSHIP]] were not even used.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-4.png)

There are two important reasons for this behavior:

- [[USERELATIONSHIP]] does not filter the model, it only changes the active relationship. Therefore, unless there are other filter arguments that are in charge of modifying the filter context, [[USERELATIONSHIP]] by itself does not change the filter context. Therefore, no filtering is happening.
- The context transition already took place prior to [[USERELATIONSHIP]] being activated. Therefore, [[USERELATIONSHIP]] cannot change the way the context transition is performed.

The steps executed by [[CALCULATE]] follow a very precise order. We outlined the correct evaluation order of [[CALCULATE]] here: [[CALCULATE|CALCULATE – DAX Guide]]. Specifically, the context transition happens before the application of [[CALCULATE]] modifiers. [[USERELATIONSHIP]] being a modifier, it is applied after the context transition. In other words, the context transition happens when the default relationship is still active and [[USERELATIONSHIP]] serves no purpose at that point.

This last formula is one step in the right direction anyway. We would like to modify the order in which [[CALCULATE]] performs its steps, so to apply the context transition after [[USERELATIONSHIP]]. Unfortunately, this is not possible. With that said, there is an opportunity: we can create a filter argument in [[CALCULATE]] that applies the same filters that the context transition would. Being a filter argument, it is applied after [[USERELATIONSHIP]] because filter arguments in [[CALCULATE]] are applied after the modifiers. The context transition filters all the columns in the expanded *Sales* table; therefore, we mimic the context transition by using the *Sales* table as a filter argument, making it happen when we need it. Here is another (still wrong) attempt:

```dax


DeliveryYear = 

CALCULATE ( 

    SELECTEDVALUE ( 'Date'[Year] ),

    Sales,

    USERELATIONSHIP ( Sales[DeliveryDate], 'Date'[Date] )

)

```

This time, the formula produces blanks everywhere.

![](https://cdn.sqlbi.com/wp-content/uploads/image5-2.png)

The reason still lies in the order in which the arguments of [[CALCULATE]] are evaluated, and also in the subtle difference between evaluating and applying a filter argument. *Sales* being a filter argument, it is applied after [[USERELATIONSHIP]] but it is evaluated before both [[USERELATIONSHIP]] and context transition (see, again, [[CALCULATE|CALCULATE – DAX Guide]] for more details on this). Therefore, the reference to *Sales* – being executed in a row context and no filter context – contains all the rows in *Sales*, not only the current row. As such, it overrides the context transition and it makes all the rows in *Sales* visible. As a consequence, there will be no single value for the *Year* column. Instead, all the years referenced from *Sales* are made visible, and [[SELECTEDVALUE]] returns a blank.

Because we want *Sales* to show only the current row, we need to embed the *Sales* filter inside its own [[CALCULATETABLE]], in order to force the context transition. That context transition – no matter what – still happens with the default active relationship. But at least, we can limit *Sales* to show only one row. [[USERELATIONSHIP]] will then change the active relationship so that the result of the filter expands to *Date* through *Sales[DeliveryDate]*. By doing so, the result of [[CALCULATETABLE]] will filter *Date* as we want it to.

Let us recap the steps required:

1. Force a context transition of *Sales*, so to show only the current row. This context transition still happens with the default relationship active.
2. Change the active relationship, in order to activate the desired relationship.
3. Evaluate *Sales* (that now shows only one row), and make it expand to *Date* through Sales*[DeliveryDate]*.
4. Use the result of the last step as a filter argument in the outer [[CALCULATE]].

All this reasoning moves us closer to the correct result; but we are not there yet. Indeed, the following formula is still incorrect and it produces a curious result, which will lead us to the final step:

```dax


DeliveryYear = 

CALCULATE ( 

    SELECTEDVALUE ( 'Date'[Year] ),

    CALCULATETABLE ( 

        Sales,

        USERELATIONSHIP ( Sales[DeliveryDate], 'Date'[Date] )

    )

)

```

The result is in the next figure. Please note that some of the rows contain the correct delivery years, whereas other rows are blank. We placed a red box to highlight the row we will discuss further to find the solution.

![](https://cdn.sqlbi.com/wp-content/uploads/image6-1.png)

You should notice that the only rows with a delivery year are the ones where *OrderDate* is the same as *DeliveryDate*. For those rows, the result is correct. All the other rows where *OrderDate* is not equal to *DeliveryDate* contain a blank. Besides, this is precisely the reason why we used this table as a demo with a small number of rows. Detecting the same pattern in a real-world database would be nearly impossible, making this complex topic even harder.

For this last step, this is where the going really gets tough; you need to pay extreme attention to the following explanation. Focus on the boxed row, where *OrderDate* is 12/26/2020 and *DeliveryDate* is 12/27/2020. We call them ***26*** and ***27*** for short, for the order and the delivery dates respectively. In order to understand why the delivery year is blank, we need to follow the evolution of the filter context step by step. Specifically, we are interested in understanding the active filter on the *Date[Date]* column.

Here is the code of the calculated column with comments. Later on we describe in further details how to read them:

```dax


--

-- Order Date = 26

-- Delivery Date = 27

--

Delivery Year =

    --

    --  Here we have a row context, Date[Date] is not filtered

    --

    CALCULATE (

        VALUES ( 'Date'[Year] ),

        --

        --  CALCULATETABLE performs a context transition on the expanded table.

        --  During the context transition, the default relationship is active.

        --  Therefore, Date[Date] is 26

        --

        CALCULATETABLE ( 

            --

            --  Date[Date] is still filtering 26. But the active relationship

            --  is now the one with Delivery Date, which contains 27.

            --  The filter on Date[Date] came from Order Date (26) but, because

            --  we have now changed the active relationship, it is working against the 

            --  Delivery Date, which contains 27.

            --

            --  Because 26 is not equal to 27, Sales is empty

            --

            Sales,

            --

            --  USERELATIONSHIP changes the active relationship, but it

            --  does not change the filter context

            --

            USERELATIONSHIP ( Sales[DeliveryDate], 'Date'[Date] )

        )

    )

```

- We start with the row context; *OrderDate* is 26, *DeliveryDate* is 27, *Date[Date]* has no filters.
- The outer [[CALCULATE]] – as its first step – evaluates its filter arguments. There is only one filter argument, which is our [[CALCULATETABLE]]. Therefore, [[CALCULATETABLE]] is evaluated.
- The inner [[CALCULATETABLE]] performs a context transition, showing only one row. This context transition happens when the active relationship is still the default one with *OrderDate*. *Date[Date]* is being filtered with 26, the order date. The reason is that *Sales* still expands to *Date* through *OrderDate*.
- [[USERELATIONSHIP]] activates the desired relationship, so that *Sales* now expands to *Date* using *DeliveryDate* to follow the relationship. [[USERELATIONSHIP]] does not change the filter, it only changes the active relationship. Therefore, *Date[Date]* is still 26.
- *Sales* is evaluated with the *DeliveryDate* relationship in place. What is the filter context when *Sales* is being evaluated? *Date[Date]* is still 26. Indeed, we have changed the relationship, but we did not change the filter on *Date[Date]*. Therefore, the filter on *Date[Date]* is filtering all the rows in *Sales* where *DeliveryDate* (not *OrderDate*) is 26. Unfortunately, the *DeliveryDate* column in the current row (the only visible row) contains 27. As such, the row is hidden by the current filter context. Therefore, the evaluation of *Sales* results in an empty table. *Date[Date]* is still 26.
- Once we use the result of the inner [[CALCULATETABLE]] as a filter in the outer [[CALCULATE]], the result is empty. Hence, the blank.

There is only one specific case which happens when *OrderDate* is identical to *DeliveryDate*. In that case, the filter on *OrderDate* filters the same date as the *DeliveryDate*, and the row still remains visible. In such cases and only then does *Sales* return the current row and perform the right calculation.

What we need to do is remove the filter starting from *OrderDate* and reaching *Date*, so that *Date* shows all the rows and is ready to accept the filter coming from the newly-activated relationship. This can be accomplished with this last (and finally correct) version of the code:

```dax


DeliveryYear = 

CALCULATE ( 

    SELECTEDVALUE ( 'Date'[Year] ),

    CALCULATETABLE ( 

        Sales,

        USERELATIONSHIP ( Sales[DeliveryDate], 'Date'[Date] ),

        REMOVEFILTERS ( ‘Date’ )

    )

)

```

This time, the result is the one we expected.

![](https://cdn.sqlbi.com/wp-content/uploads/image7-1.png)

The last [[REMOVEFILTERS]] removes the effect of context transition on the *Date* table. It still keeps the context transition on *Sales*, which is what we want. The result of *Sales* now contains a filter for *Date* that follows the relationship activated by [[USERELATIONSHIP]]; the calculation provides the correct result.

As you have seen, this final version of the code is not extremely complex. Nonetheless, the lines of the formula each have a specific purpose: they all interact together to produce the right result. Despite working, this code is extremely fragile. Changing a tiny bit of the formula might easily disrupt the delicate harmony among the many components.

Consequently, the best piece of advice we could give our readers is to stay as far away as possible from such high levels of complexity. Understanding how this code works is a beautiful exercise for the mind, as it involves all of the most complex concepts in DAX. The very same reason why you should never put this code in production.

If you need to retrieve the order date from the delivery date in a calculated column, the easiest way is to rely on [[LOOKUPVALUE]]. Yes, [[YEAR]] ( *Sales[OrderDate]* ) would work in this specific example, but [[LOOKUPVALUE]] is the more generic replacement of [[USERELATIONSHIP]] in a calculated column. Look how simple the *DeliveryYear* calculated column is, when using [[LOOKUPVALUE]]:

```dax


DeliveryYear =

    LOOKUPVALUE(

        'Date'[Year],

        'Date'[Date], Sales[DeliveryDate]

    )

```

[[LOOKUPVALUE]] does not rely on relationships, therefore it is slower than a regular relationship. But this is true only if you can use a relationship by just using [[RELATED]]. If [[RELATED]] is not an option, as it is the case for the non-default relationships in calculated columns, then [[LOOKUPVALUE]] is an absolute winner. It is simpler, faster, more reliable and more robust.

[[USERELATIONSHIP]]

CALCULATE modifier

Specifies an existing relationship to be used in the evaluation of a DAX expression. The relationship is defined by naming, as arguments, the two columns that serve as endpoints.

`USERELATIONSHIP ( <ColumnName1>, <ColumnName2> )`

[[LOOKUPVALUE]]

Retrieves a value from a table.

`LOOKUPVALUE ( <Result_ColumnName>, <Search_ColumnName>, <Search_Value> [, <Search_ColumnName>, <Search_Value> [, … ] ] [, <Alternate_Result>] )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[CALENDARAUTO]]

Returns a table with one column of dates calculated from the model automatically.

`CALENDARAUTO ( [<FiscalYearEndMonth>] )`

[[YEAR]]

Returns the year of a date as a four digit integer.

`YEAR ( <Date> )`

[[RELATED]]

Returns a related value from another table.

`RELATED ( <ColumnName> )`

[[SELECTEDVALUE]]

Returns the value when there’s only one value in the specified column, otherwise returns the alternate result.

`SELECTEDVALUE ( <ColumnName> [, <AlternateResult>] )`

[[CALCULATETABLE]]

Context transition

Evaluates a table expression in a context modified by filters.

`CALCULATETABLE ( <Table> [, <Filter> [, <Filter> [, … ] ] ] )`

[[REMOVEFILTERS]]

CALCULATE modifier

Clear filters from the specified tables or columns.

`REMOVEFILTERS ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`
