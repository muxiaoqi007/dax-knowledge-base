---
title: "Introducing LOOKUPVALUE"
url: "https://www.sqlbi.com/articles/introducing-lookupvalue"
published: "2020-08-17"
updated: 
source: "sqlbi.com"
---

# Introducing LOOKUPVALUE

> 发布：2020-08-17

[[LOOKUPVALUE]] is one of the most widely used functions, especially for DAX developers who come from an Excel background. Indeed, the behavior of [[LOOKUPVALUE]] is very close to the behavior of the widely-adopted VLOOKUP function in Excel. Yet, there are important differences between the two; quite often, newbies use [[LOOKUPVALUE]] instead of creating a relationship between tables that ensures higher flexibility and higher performance.

This article is an introduction to [[LOOKUPVALUE]]. If you are interested in studying the internals of [[LOOKUPVALUE]] in more detail, make sure to check the advanced [[LOOKUPVALUE]] article; in that article, we describe the behavior of the function in detail along with several performance considerations.

[[LOOKUPVALUE]] searches a table for the value of a column, given a set of values for other columns in the same table. For example, if you want to retrieve the *Product[Unit Price]* column from the *Product* table, where the *Product[ProductKey]* column has the same values as the *Sales[ProductKey]* column, you can create a calculated column with the following formula:

```dax


Unit Price =

LOOKUPVALUE (

    'Product'[Unit Price],

    'Product'[ProductKey], Sales[ProductKey]

)

```

The first argument is the column containing the value to retrieve. The second argument is the column to search in, which needs to be in the same table as the first argument; and the third argument is the value you want to search for, which can be any expression.  
![](https://cdn.sqlbi.com/wp-content/uploads/Introducing-LOOKUPVALUE-01.png)

The second and third arguments form a pair that can be repeated multiple times. For example, to retrieve the *Rate* in a specific date and for a specific currency, you can write multiple conditions as in the following calculated column:

```dax


ExchangeRateToEUR =

LOOKUPVALUE (

    'Daily Exchange Rates'[Rate],

    'Daily Exchange Rates'[CurrencyKey], LOOKUPVALUE (

        'Currency'[CurrencyKey],

        'Currency'[Currency Code], "EUR"

    ),

    'Daily Exchange Rates'[Date], Sales[Order Date]

)

```

As a best practice, both for readability and performance, you should use variables for the values to search for, as in the following variation of the same calculated column:

```dax


ExchangeRateToEUR =

VAR CurrencyKey =

    LOOKUPVALUE (

        'Currency'[CurrencyKey],

        'Currency'[Currency Code], "EUR"

    )

VAR CurrentDate = Sales[Order Date]

VAR Result =

    LOOKUPVALUE (

        'Daily Exchange Rates'[Rate],

        'Daily Exchange Rates'[CurrencyKey], CurrencyKey,

        'Daily Exchange Rates'[Date], CurrentDate

    )

RETURN

    Result

```

When providing the column to search into, you can use any column of the expanded table. If you are not familiar with expanded tables, you should read the [Expanded tables in DAX](https://www.sqlbi.com/articles/expanded-tables-in-dax/) article. This is useful in order to avoid multiple calls to [[LOOKUPVALUE]]. Indeed, the previous calculated column can be better written by using the expanded table, directly searching into the *Currency[Currency Code]* column:

```dax


ExchangeRateToEUR =

VAR CurrentDate = Sales[Order Date]

VAR Result =

    LOOKUPVALUE (

        'Daily Exchange Rates'[Rate],

        'Currency'[Currency Code], "EUR",

        'Daily Exchange Rates'[Date], CurrentDate

    )

RETURN

    Result

```

[[LOOKUPVALUE]] looks for a single value satisfying the conditions in the target table. In case the search returns multiple values, you obtain an obscure error message saying: “A table of multiple values was provided where a single value was expected”. If that happens, you need to be more specific with your search list, making sure a single row is identified by [[LOOKUPVALUE]].

After the list of pairs, you can also provide the default value as the last argument. In case [[LOOKUPVALUE]] does not find a suitable matching row, it returns the default value. [[LOOKUPVALUE]] detects that the last argument is the default value if it does not belong to a pair – that is, there are no other arguments after it.

If your search list is made up of only one column, then [[LOOKUPVALUE]] is pretty much never your best option. Indeed, when searching for a single column, a relationship is always better: it is faster and provides a clearer structure to the model. When on the other hand you search for multiple columns, then [[LOOKUPVALUE]] comes in handy.

Another scenario where [[LOOKUPVALUE]] is preferable over a relationship in the model is when the condition you set is not a single column, but instead a more complex condition based on multiple columns. In that case, [[LOOKUPVALUE]] provides greater flexibility than a relationship.

Finally, [[LOOKUPVALUE]] is invaluable when you create in a table, a calculated column that searches for the value of a column in that table. Indeed, you cannot create a relationship between a table and itself, whereas you can use [[LOOKUPVALUE]] to search within the current table.

This last example demonstrates the last two points. In the *Sales* table we store the *Net Price*, which is the price at the time of the sale. What if we want to compare the price currently being looked at in the computation, with the second latest price for that product? Provided that you have a column storing the order position – as in, the first order of a product is ranked 1, the second order of that same product is ranked 2 and so on; then the following calculated column in *Sales* retrieves the price variation by using [[LOOKUPVALUE]] to retrieve the net price from the previous order of that product:

```dax


Price Variation = 

VAR PreviousRankOrder = Sales[RankOrder] - 1

VAR CurrentProduct = Sales[ProductKey]

VAR CurrentNetPrice = Sales[Net Price]

VAR PreviosNetPrice = 

    COALESCE ( 

        LOOKUPVALUE (

            Sales[Net Price],

            Sales[RankOrder], PreviousRankOrder,

            Sales[ProductKey], CurrentProduct

        ),

        Sales[Net Price]

    )

VAR PriceVariation = CurrentNetPrice - PreviosNetPrice

RETURN

    PriceVariation

```

With that said, relying on [[LOOKUPVALUE]] too much in your code is a symptom of a sub-optimal model. If you find yourself overusing [[LOOKUPVALUE]], then it is a good idea to review the model to check that you are not using [[LOOKUPVALUE]] to mimic relationships. If that is the case, building the correct set of relationships leads to a better model and better performance.

As a final note, you can appreciate that in the last example provided in the article, we used [[COALESCE]] instead of the default value for [[LOOKUPVALUE]]. The goal is performance, and we analyze the details of [[LOOKUPVALUE]]’s performance in a future article.

[[LOOKUPVALUE]]

Retrieves a value from a table.

`LOOKUPVALUE ( <Result_ColumnName>, <Search_ColumnName>, <Search_Value> [, <Search_ColumnName>, <Search_Value> [, … ] ] [, <Alternate_Result>] )`

[[COALESCE]]

Returns the first argument that does not evaluate to a blank value. If all arguments evaluate to blank values, BLANK is returned.

`COALESCE ( <Value1>, <Value2> [, <ValueN> [, … ] ] )`
