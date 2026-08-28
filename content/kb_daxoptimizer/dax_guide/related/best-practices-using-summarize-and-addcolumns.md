---
title: "Best practices using SUMMARIZE and ADDCOLUMNS"
url: "https://www.sqlbi.com/articles/best-practices-using-summarize-and-addcolumns"
published: "2023-03-17"
updated: 
source: "sqlbi.com"
---

# Best practices using SUMMARIZE and ADDCOLUMNS

> 发布：2023-03-17

**UPDATE 2022-02-11 :** The article has been updated using DAX.DO for the sample queries and removing the outdated part.  
**UPDATE 2023-03-17 :** Fixed an incorrect description before example #11.

Everyone using DAX is probably used to SQL query language. Because of the similarities between Tabular data modeling and relational data modeling, there is the expectation that you can perform the same operations as those allowed in SQL. However, in its current implementation DAX does not permit all the operations that you can perform in SQL. This article describes how to use [[ADDCOLUMNS]] and [[SUMMARIZE]], which can be used in any DAX expression, including measures. For DAX queries, you should consider using [[SUMMARIZECOLUMNS]], starting with the Introducing [[SUMMARIZECOLUMNS]] article. You can also read the All the secrets of Summarize article for more insights about inner workings of [[SUMMARIZE]].

## Extension Columns

Extension columns are columns that you add to existing tables. You can obtain extension columns by using both [[ADDCOLUMNS]] and [[SUMMARIZE]]. For example, the following query adds an *Open Year* column to the rows returned from the *Store* table.

```dax


EVALUATE 

ADDCOLUMNS (

    Store, 

    "Open Year", YEAR ( Store[Open Date] )

)

```

| StoreKey | Store Code | Country | State | Name | Square Meters | Open Date | Close Date | Status | Open Year |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 10 | 1 | Australia | Australian Capital Territory | Contoso Store Australian Capital Territory | 595 | 2008-01-01 | (Blank) | (Blank) | 2,008 |
| 20 | 2 | Australia | Northern Territory | Contoso Store Northern Territory | 665 | 2008-01-12 | 2016-07-07 | Closed | 2,008 |
| 30 | 3 | Australia | South Australia | Contoso Store South Australia | 2,000 | 2012-01-07 | 2015-08-08 | Restructured | 2,012 |
| 35 | 3 | Australia | South Australia | Contoso Store South Australia | 3,000 | 2015-12-08 | (Blank) | (Blank) | 2,015 |
| … | … | … | … | … | … | … | … | … | … |

You can also create an extension column by using [[SUMMARIZE]]. For example, you can count the number of stores for each country by using the following query (please note that this query is not a best practice – you will see why later in this article).

```dax


EVALUATE

SUMMARIZE (

    Store,

    Store[Country],

    "Stores", COUNTROWS( Store )

)

```

| Country | Stores |
| --- | --- |
| Australia | 7 |
| Canada | 7 |
| France | 7 |
| Germany | 10 |
| Italy | 3 |
| Netherlands | 5 |
| United Kingdom | 7 |
| United States | 27 |
| Online | 1 |

In practice, an extension column is a calculated column created within the query.

## Query Projection

In a SELECT statement in SQL, you can choose the column projected in the result, whereas in DAX you can only add columns to a table by creating extension columns. The only workaround available is to use [[SUMMARIZE]] to group the table by the columns you want to obtain in the output. As long as you do not need to see duplicated rows in the result, this solution does not have particular side effects. For example, if you want to get just the list of store names and their corresponding open date, you can write the following query.

```dax


EVALUATE

SUMMARIZE (

    Store,

    Store[Name],

    Store[Open Date]

)

```

| Name | Open Date |
| --- | --- |
| Contoso Store Australian Capital Territory | 2008-01-01 |
| Contoso Store Northern Territory | 2008-01-12 |
| Contoso Store South Australia | 2012-01-07 |
| Contoso Store South Australia | 2015-12-08 |
| … | … |

Whenever you can create an extended column by using both [[ADDCOLUMNS]] and [[SUMMARIZE]], you should always favor [[ADDCOLUMNS]] for performance reasons. For example, you can add the open year by using one of two techniques. First, you can just use [[SUMMARIZE]].

```dax


EVALUATE

SUMMARIZE (

    Store,

    Store[Name],

    Store[Open Date], 

    "Open Year", YEAR ( Store[Open Date] )

)

```

| Store[Name] | Store[Open Date] | Open Year |
| --- | --- | --- |
| Contoso Store Australian Capital Territory | 2008-01-01 | 2,008 |
| Contoso Store Northern Territory | 2008-01-12 | 2,008 |
| Contoso Store South Australia | 2012-01-07 | 2,012 |
| Contoso Store South Australia | 2015-12-08 | 2,015td> |
| … | … | … |

Second, you can use [[ADDCOLUMNS]] adding the Year Production column to the [[SUMMARIZE]] result.

```dax


EVALUATE

ADDCOLUMNS (

    SUMMARIZE (

        Store,

        Store[Name],

        Store[Open Date]

    ),

    "Open Year", YEAR ( Store[Open Date] )

)

```

| Store[Name] | Store[Open Date] | Open Year |
| --- | --- | --- |
| Contoso Store Australian Capital Territory | 2008-01-01 | 2,008 |
| Contoso Store Northern Territory | 2008-01-12 | 2,008 |
| Contoso Store South Australia | 2012-01-07 | 2,012 |
| Contoso Store South Australia | 2015-12-08 | 2,015 |
| … | … | … |

Both queries produce the same result.

However, you should always favor the [[ADDCOLUMNS]] version. The rule of thumb is that you should never add extended columns by using [[SUMMARIZE]], unless it is required due to at least one of the following conditions:

- You want to use [[ROLLUP]] over one or more grouping columns in order to obtain subtotals
- You are using non-trivial table expressions in the extended column, as you will see in the “Filter Context in [[SUMMARIZE]] and [[ADDCOLUMNS]]” section later in this article

The best practice is that, whenever possible, instead of writing

```dax


SUMMARIZE( <table>, <group_by_column>, <column_name>, <expression> )
```

you should write:

```dax


ADDCOLUMNS(

    SUMMARIZE( <table>, <group by column> ),

    <column_name>, CALCULATE( <expression> )

)

```

The [[CALCULATE]] you can see in the best practices template above is not always required, but you need it whenever the <expression> contains an aggregation function. The reason is that [[ADDCOLUMNS]] operates in a row context that does not automatically propagate into a filter context, whereas the same <expression> within a [[SUMMARIZE]] is executed into a filter context corresponding to the values in the grouped columns. The previous examples used a scalar expression over a column that was included in the [[SUMMARIZE]] output, so the reference to the column value was valid within the row context. Now, consider the following query that you have already seen at the beginning of this article.

```dax


EVALUATE

SUMMARIZE (

    Store,

    Store[Country],

    "Stores", COUNTROWS( Store )

)

```

If you rewrite this query by simply moving the Stores extended columns out of the [[SUMMARIZE]] into an [[ADDCOLUMNS]] function, you obtain the following query that produces the wrong result. This is because it returns the number of rows in the entire Store table for each row of the result instead of returning the number of stores for each country.

```dax


EVALUATE

ADDCOLUMNS (

    SUMMARIZE (

        Store,

        Store[Country]

    ),

    "Stores", COUNTROWS ( Store )

)

```

| Country | Stores |
| --- | --- |
| Australia | 74 |
| Canada | 74 |
| France | 74 |
| Germany | 74 |
| Italy | 74 |
| Netherlands | 74 |
| United Kingdom | 74 |
| United States | 74 |
| Online | 74 |

In order to obtain the result you want, you have to wrap the expression for the Stores extended column within a [[CALCULATE]] statement. This way, the row context for *Store[Country]* is transformed into a filter context and the [[COUNTROWS]] function only considers the stores belonging to the country of the current row.

```dax


EVALUATE

ADDCOLUMNS (

    SUMMARIZE (

        Store,

        Store[Country]

    ),

    "Stores", CALCULATE ( COUNTROWS ( Store ) )

)

```

| Country | Stores |
| --- | --- |
| Australia | 7 |
| Canada | 7 |
| France | 7 |
| Germany | 10 |
| Italy | 3 |
| Netherlands | 5 |
| United Kingdom | 7 |
| United States | 27 |
| Online | 1 |

Thus, as a rule of thumb, **wrap any expression for an extended column within a [[CALCULATE]] function whenever you move an extended column out from [[SUMMARIZE]] into an ADDCOLUMN statement**. Just pay attention to the caveats in the following section!

## Filter Context in [[SUMMARIZE]] and [[ADDCOLUMNS]]

By describing the pattern of creating extended columns using [[ADDCOLUMNS]] instead of [[SUMMARIZE]] we mentioned that there are conditions in which you cannot do this substitution – the result would be incorrect. For example, when you apply filters over columns that are not included in the grouped column and then calculate the extended column expression using data coming from related tables, the filter context will be different between [[SUMMARIZE]] vs. [[ADDCOLUMNS]].

The following query returns – by Product Category and Customer Country – the profit made by the top 2 customers for each product. Thus, a category might contain several customers, but no more than 2 per product:

```dax


EVALUATE

SUMMARIZE (

    GENERATE (

        'Product',

        TOPN (

            2,

            Customer,

            [Sales Amount]

        )

    ),

    Product[Category],

    Customer[Country],

    "Margin", [Margin]

)

ORDER BY [Margin] DESC

```

| Product[Category] | Customer[Country] | Margin |
| --- | --- | --- |
| Computers | United States | 711,304.76 |
| Home Appliances | United States | 401,760.06 |
| TV and Video | United States | 288,672.69 |
| Cell phones | United States | 205,773.77 |
| … | … | … |

In this case, applying the pattern of moving the extended columns out of a [[SUMMARIZE]] into an [[ADDCOLUMNS]] does not work, because the [[GENERATE]] used as a parameter of the [[SUMMARIZE]] returns only a few products and customers – while the [[SUMMARIZE]] only considers the sales related to these combinations of products and customers. Consider the following query and its result:

```dax


EVALUATE

ADDCOLUMNS (

    SUMMARIZE (

        GENERATE (

            'Product',

            TOPN (

                2,

                Customer,

                [Sales Amount]

            )

        ),

        Product[Category],

        Customer[Country]

    ),

    "Margin", [Margin]

)

ORDER BY [Margin] DESC

```

| Product[Category] | Customer[Country] | Margin |
| --- | --- | --- |
| Computers | United States | 1,440,461.21 |
| Cell phones | United States | 612,576.04 |
| Home Appliances | United States | 537,585.70 |
| TV and Video | United States | 498,765.37 |
| … | Cana…da | … |

As you can see, the results are different as *Margin* is higher than the initial result. This is because this query is computing the *Margin* measure in a filter context that filters only *Product[Category]* and *Customer[Country]*, ignoring the filter of the top 2 customers that used in the table argument of [[SUMMARIZE]], where the result of GENERATED included all the columns of *Product* and *Customer* but just for the top 2 customers for each country. Only these customers were considered in the *Margin* calculation inside [[SUMMARIZE]], because [[SUMMARIZE]] was using only those customers filtered by [[TOPN]].

If you wrap the [[SUMMARIZE]] into an [[ADDCOLUMNS]], the extended columns created in [[ADDCOLUMNS]] work on a filter context defined by *Product[Category]* and *Customer[Country]*, considering many more sales than those originally used by the initial query. Thus, in order to generate the equivalent result by using [[ADDCOLUMNS]], it is necessary to bring the same filter obtained by [[GENERATE]] in the following *Margin* measure evaluation. We can do that by storing the result of GENERATED in the *ProductsCustomers* variable that we reference in [[SUMMARIZE]] and we pass as a filter argument to [[CALCULATE]] to evaluate the *Margin* measure in [[ADDCOLUMNS]]. The use of [[KEEPFILTERS]] is crucial to keep the result of the context transition and filter by *Product[Category]* and *Customer[Country]* within the filter stored in *ProductsCustomers*.  
This is the equivalent DAX query using [[ADDCOLUMNS]] for generating the extended column:

```dax


EVALUATE

VAR ProductsCustomers =

    GENERATE (

        'Product',

        TOPN (

            2,

            Customer,

            [Sales Amount]

        )

    )

RETURN

    ADDCOLUMNS (

        SUMMARIZE (

            ProductsCustomers,

            Product[Category],

            Customer[Country]

        ),

        "Margin",

            CALCULATE (

                [Margin],

                KEEPFILTERS ( ProductsCustomers )

            )

    )

ORDER BY [Margin] DESC

```

| Product[Category] | Customer[Country] | Margin |
| --- | --- | --- |
| Computers | United States | 711,304.76 |
| Home Appliances | United States | 401,760.06 |
| TV and Video | United States | 288,672.69 |
| Cell phones | United States | 205,773.77 |
| … | … | … |

The conclusion is that **extended columns in a [[SUMMARIZE]] expression should not be moved out to an [[ADDCOLUMNS]] if the table used in [[SUMMARIZE]] has particular filters and the extended column expression uses columns that are not part of the output**. Even though you can create an equivalent [[ADDCOLUMNS]] query, the result is more complex and there are no performance benefits in this refactoring. The more complex query has the same (not so good) performance as the [[SUMMARIZE]] query – both queries in this section require almost 10 seconds to run on the sample Contoso database.

[[ADDCOLUMNS]]

Returns a table with new columns specified by the DAX expressions.

`ADDCOLUMNS ( <Table>, <Name>, <Expression> [, <Name>, <Expression> [, … ] ] )`

[[SUMMARIZE]]

Creates a summary of the input table grouped by the specified columns.

`SUMMARIZE ( <Table> [, <GroupBy_ColumnName> [, [<Name>] [, [<Expression>] [, <GroupBy_ColumnName> [, [<Name>] [, [<Expression>] [, … ] ] ] ] ] ] ] )`

[[SUMMARIZECOLUMNS]]

Create a summary table for the requested totals over set of groups.

`SUMMARIZECOLUMNS ( [<GroupBy_ColumnName> [, [<FilterTable>] [, [<Name>] [, [<Expression>] [, <GroupBy_ColumnName> [, [<FilterTable>] [, [<Name>] [, [<Expression>] [, … ] ] ] ] ] ] ] ] ] )`

[[ROLLUP]]

Identifies a subset of columns specified in the call to SUMMARIZE function that should be used to calculate subtotals.

`ROLLUP ( <GroupBy_ColumnName> [, <GroupBy_ColumnName> [, … ] ] )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[COUNTROWS]]

Counts the number of rows in a table.

`COUNTROWS ( [<Table>] )`

[[GENERATE]]

The second table expression will be evaluated for each row in the first table. Returns the crossjoin of the first table with these results.

`GENERATE ( <Table1>, <Table2> )`

[[TOPN]]

Returns a given number of top rows according to a specified expression.

`TOPN ( <N_Value>, <Table> [, <OrderBy_Expression> [, [<Order>] [, <OrderBy_Expression> [, [<Order>] [, … ] ] ] ] ] )`

[[KEEPFILTERS]]

CALCULATE modifier

Changes the CALCULATE and CALCULATETABLE function filtering semantics.

`KEEPFILTERS ( <Expression> )`
