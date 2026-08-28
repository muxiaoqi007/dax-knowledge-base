---
title: "Lookup multiple values in DAX"
url: "https://www.sqlbi.com/articles/lookup-multiple-values-in-dax"
published: "2020-12-21"
updated: 
source: "sqlbi.com"
---

# Lookup multiple values in DAX

> 发布：2020-12-21

There are a number of scenarios in DAX where you need a value from a ”lookup” table that is not connected through a relationship (which would enable the use of [[RELATED]] function). For example, consider the following two tables.

The **Sales** table contains a number of transactions:

|  |  |  |  |
| --- | --- | --- | --- |
| **Customer** | **Product** | **Date** | **Quantity** |
| Marco | Mouse | 1/20/2017 | 2 |
| Marco | Tablet | 2/16/2017 | 1 |
| Alberto | Mouse | 1/30/2017 | 1 |
| Alberto | Tablet | 1/30/2017 | 1 |
| Alberto | Watch | 2/23/2017 | 1 |

The **Promo** table contains a number of promotions, with a primary key that corresponds to Month and Product.

|  |  |  |  |
| --- | --- | --- | --- |
| **Month** | **Product** | **Campaign** | **Media** |
| 1 | Mouse | Bundle | Radio |
| 1 | Tablet | Bundle | Banner |
| 1 | Watch | Two-for-one | Newsletter |
| 2 | Mouse | Sale | Magazine |
| 2 | Watch | Sale | Newsletter |

If you create both columns Campaign and Media for each Sales transaction in a table expression in DAX, you might use the following approach, which corresponds to what you would write in two calculated columns in the Sales table.

```dax


EVALUATE

ADDCOLUMNS (

    Sales,

    "Campaign",

    VAR MonthNumber =

        MONTH ( Sales[Date] )

    RETURN

        LOOKUPVALUE (

            Promo[Campaign],

            Promo[Product], Sales[Product],

            Promo[Month], MonthNumber

        ),

    "Media",

    VAR MonthNumber =

        MONTH ( Sales[Date] )

    RETURN

        LOOKUPVALUE (

            Promo[Media],

            Promo[Product], Sales[Product],

            Promo[Month], MonthNumber

        )

)

```

[![](https://cdn.sqlbi.com/wp-content/uploads/LookupValues-01.png)](https://cdn.sqlbi.com/wp-content/uploads/LookupValues-01.png)

The [[LOOKUPVALUE]] function retrieves the two values, Campaign and Media. As you can see, there is a large amount of code duplicated for the two columns. Also from a performance point of view, the engine creates two different and independent subqueries to retrieve the values of the two columns. The situation worsens if you need more columns.

## Using [[GENERATE]] and [[ROW]]

You can save some line of code and improve the performance by using an approach based on [[GENERATE]] and [[ROW]] functions.

```dax


EVALUATE

GENERATE (

    Sales,

    CALCULATETABLE (

        ROW ( 

            "Campaign", VALUES ( Promo[Campaign] ), 

            "Media", VALUES ( Promo[Media] ) 

        ),

        TREATAS ( ROW ( "Month", MONTH ( Sales[Date] ) ), Promo[Month] ),

        TREATAS ( ROW ( "Product", Sales[Product] ), Promo[Product] )

    )

)

```

The [[TREATAS]] transfers the filter context from the current row of Sales to the Promo table. The presence of [[VALUES]] in the [[ROW]] function guarantees that in case of multiple results, the query fails, just as [[LOOKUPVALUE]] does (you don’t want to provide wrong results if there is bad data).  
By using [[ROW]] we guarantee that there is always a row, even when there are no matching rows in the Promo table. This is important, because we want to display a blank value for Campaign and Media in case there are no rows found in Promo for a particular transaction in Sales. By using [[GENERATE]], a row in Sales would be removed from the result in case the second argument of [[GENERATE]] would return no rows. Keep it in mind looking at the following example.

## Using [[GENERATEALL]] and [[SELECTCOLUMNS]]

If you can trust your data and you know that for a given combination of month and product there could be no more than one row in Promo, you can use this other syntax, which is also faster:

```dax


EVALUATE

GENERATE (

    Sales,

    CALCULATETABLE (

        SELECTCOLUMNS ( 

            Promo, 

            "Campaign", Promo[Campaign], 

            "Media", Promo[Media] 

        ),

        TREATAS ( ROW ( "Month", MONTH ( Sales[Date] ) ), Promo[Month] ),

        TREATAS ( ROW ( "Product", Sales[Product] ), Promo[Product] )

    )

)

```

[![](https://cdn.sqlbi.com/wp-content/uploads/LookupValues-02.png)](https://cdn.sqlbi.com/wp-content/uploads/LookupValues-02.png)

In this case, all the corresponding rows in the Promo table are returned, and [[SELECTCOLUMNS]] only returns the desired Campaign and Media columns, hiding the Month and Product columns that would just be redundant. You can use this approach as a way to join two tables using multiple columns. However, by using [[GENERATE]] you do not see in the result the rows in Sales that have no corresponding rows in Promo. In fact, the previous result has only four rows instead of five.

The next example uses [[GENERATEALL]] instead of [[GENERATE]], so the result will contain all the rows from Sales, even when there are no corresponding rows in Promo. The [[GENERATEALL]] function was not necessary in previous examples, because the [[ROW]] function always returns a single row.

```dax


EVALUATE

GENERATEALL (

    Sales,

    CALCULATETABLE (

        SELECTCOLUMNS ( 

            Promo, 

            "Campaign", Promo[Campaign], 

            "Media", Promo[Media] 

        ),

        TREATAS ( ROW ( "Month", MONTH ( Sales[Date] ) ), Promo[Month] ),

        TREATAS ( ROW ( "Product", Sales[Product] ), Promo[Product] )

    )

)

```

[![](https://cdn.sqlbi.com/wp-content/uploads/LookupValues-03.png)](https://cdn.sqlbi.com/wp-content/uploads/LookupValues-03.png)

Finally, if you have a version of DAX that does not support [[TREATAS]], you can use [[INTERSECT]] instead (but [[TREATAS]] is the best practice, also from a performance standpoint).

```dax


EVALUATE

GENERATEALL (

    Sales,

    CALCULATETABLE (

        SELECTCOLUMNS ( 

            Promo, 

            "Campaign", Promo[Campaign], 

            "Media", Promo[Media] 

        ),

        INTERSECT ( ALL ( Promo[Month] ), ROW ( "Month", MONTH ( Sales[Date] ) ) ),

        INTERSECT ( ALL ( Promo[Product] ), ROW ( "Product", Sales[Product] ) )

    )

)

```

## Using NATURALLEFTJOIN

The join between two tables can be obtained also by using the two DAX functions [[NATURALINNERJOIN]] and NATURALLEFTJOIN. However, these functions require to join columns with the same name, type, and lineage. This latter requirement does not allow using native columns of the model, so you have to remove the data lineage from the columns involved in the join, for instance by using an expression in [[SELECTCOLUMNS]]. In the following example, the columns Month and Product used to join the two tables do not have the same lineage of the corresponding native columns.

```dax


EVALUATE

NATURALLEFTOUTERJOIN (

    SELECTCOLUMNS (

        Sales,

        "Customer", Sales[Customer],

        "Date", Sales[Date],

        "Product", "" & Sales[Product],

        "Month", MONTH ( Sales[Date] )

    ),

    SELECTCOLUMNS (

        Promo,

        "Product", Promo[Product] & "",

        "Month", 0 + Promo[Month],

        "Campaign", Promo[Campaign],

        "Media", Promo[Media]

    )

)

```

[![](https://cdn.sqlbi.com/wp-content/uploads/LookupValues-04.png)](https://cdn.sqlbi.com/wp-content/uploads/LookupValues-04.png)

For this reason, [[NATURALINNERJOIN]] and NATURALLEFTJOIN are more useful when you create tables as a result of other table expressions that do not return native columns.

## Conclusion

In DAX you do not have a real join operator between two tables, which would be useful to retrieve data from multiple columns of a lookup table. The functions [[NATURALINNERJOIN]] and NATURALLEFTJOIN are not the best choice to join two physical tables. The [[LOOKUPVALUE]] function is a good option when you need a single column, but you can consider alternative approaches when you need to retrieve multiple columns from a lookup table.

[[RELATED]]

Returns a related value from another table.

`RELATED ( <ColumnName> )`

[[LOOKUPVALUE]]

Retrieves a value from a table.

`LOOKUPVALUE ( <Result_ColumnName>, <Search_ColumnName>, <Search_Value> [, <Search_ColumnName>, <Search_Value> [, … ] ] [, <Alternate_Result>] )`

[[GENERATE]]

The second table expression will be evaluated for each row in the first table. Returns the crossjoin of the first table with these results.

`GENERATE ( <Table1>, <Table2> )`

[[ROW]]

Returns a single row table with new columns specified by the DAX expressions.

`ROW ( <Name>, <Expression> [, <Name>, <Expression> [, … ] ] )`

[[TREATAS]]

Treats the columns of the input table as columns from other tables. For each column, filters out any values that are not present in its respective output column.

`TREATAS ( <Expression>, <ColumnName> [, <ColumnName> [, … ] ] )`

[[VALUES]]

When a column name is given, returns a single-column table of unique values. When a table name is given, returns a table with the same columns and all the rows of the table (including duplicates) with the [additional blank row](https://www.sqlbi.com/articles/blank-row-in-dax/) caused by an invalid relationship if present.

`VALUES ( <TableNameOrColumnName> )`

[[GENERATEALL]]

The second table expression will be evaluated for each row in the first table. Returns the crossjoin of the first table with these results, including rows for which the second table expression is empty.

`GENERATEALL ( <Table1>, <Table2> )`

[[SELECTCOLUMNS]]

Returns a table with selected columns from the table and new columns specified by the DAX expressions.

`SELECTCOLUMNS ( <Table> [[, <Name>], <Expression> [[, <Name>], <Expression> [, … ] ] ] )`

[[INTERSECT]]

Returns the rows of left-side table which appear in right-side table.

`INTERSECT ( <LeftTable>, <RightTable> )`

[[NATURALINNERJOIN]]

Joins the Left table with right table using the Inner Join semantics.

`NATURALINNERJOIN ( <LeftTable>, <RightTable> )`
