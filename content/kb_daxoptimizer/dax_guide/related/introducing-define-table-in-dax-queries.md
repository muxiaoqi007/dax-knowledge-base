---
title: "Introducing DEFINE TABLE in DAX queries"
url: "https://www.sqlbi.com/articles/introducing-define-table-in-dax-queries"
published: "2021-02-01"
updated: 
source: "sqlbi.com"
---

# Introducing DEFINE TABLE in DAX queries

> 发布：2021-02-01

Introduced in December 2020, the [DEFINE](https://dax.guide/st/define/) [TABLE](https://dax.guide/st/table/) statement lets you define a calculated table local to a query. The table is not persisted in the model, it exists only for the lifetime of the query. Apart from that, it is a calculated table in every sense of the term albeit with some limitations.

The extension of DAX with the capability to define calculated tables local to a query is needed in order to support composite models (DirectQuery for Power BI datasets and Azure Analysis Services). There are no limitations on the use of the feature, so you can take advantage of local tables in any DAX query. We refer to calculated tables defined in a query as *query calculated tables*, or *query tables* for short.

The syntax to define a query table is the following:

```dax


DEFINE

    TABLE ColorsBrandsAndSales =

        SUMMARIZECOLUMNS (

            'Product'[Color],

            'Product'[Brand],

            "Amt", [Sales Amount]

        )

EVALUATE

    SUMMARIZECOLUMNS ( 

        ColorsBrandsAndSales[Brand],

        "Total Amt", SUM ( ColorsBrandsAndSales[Amt] )

    )

```

In its simplicity, the above example shows several interesting features of query tables:

- You can reference columns in a query table by using the table name, like *ColorsAndSales[Brand]* and *ColorsAndSales[Amt]*.
- You can use columns in a query table as if they were model columns. Indeed, [[SUMMARIZECOLUMNS]] can use any column in a query table to perform the grouping.
- Query tables can be filtered through the filter context, as is the case in the calculation of *Total Amt* in the second [[SUMMARIZECOLUMNS]].

Indeed, query tables have their own data lineage and they can be used like any model table. However, there are some limitations in how they are created. You cannot reference a query table from another query table, neither directly nor indirectly. For example, the following query is invalid because *BrandsAndSales* is based on *ColorsBrandsAndSales* and this is not allowed:

```dax


DEFINE

    TABLE ColorsBrandsAndSales =

        SUMMARIZECOLUMNS (

            'Product'[Color],

            'Product'[Brand],

            "Amt", [Sales Amount]

        )

    TABLE BrandsAndSales =

        SUMMARIZECOLUMNS (

            ColorsBrandsAndSales[Brand],                   -- Query table not allowed

            "Total Amt", SUM ( ColorsBrandsAndSales[Amt] ) -- Query table not allowed

        )

EVALUATE

    BrandsAndSales

```

Another limitation of query tables is that they cannot be used to create additional columns with [DEFINE COLUMN](https://www.sqlbi.com/articles/introducing-define-column-in-dax-queries). The following query raises an error if executed; this is because you can use [DEFINE](https://dax.guide/st/define/) [COLUMN](https://dax.guide/st/column/) to add columns to model tables, but you cannot add columns to query tables:

```dax


DEFINE

    TABLE ColorsBrandsAndSales =

        SUMMARIZECOLUMNS (

            'Product'[Color],

            'Product'[Brand]

        )

    COLUMN ColorsBrandsAndSales[Amt] =     -- A query column cannot extend a query table

        [Sales Amount] 

EVALUATE

    ColorsBrandsAndSales

```

An important detail about query tables having their own lineage is that a query table cannot filter other tables in the model. This behavior is very different from variables. For example, the following code produces two different results. *TColors* does not filter Sales, whereas *VColors* does:

```dax


DEFINE

    TABLE TColors =

        VALUES ( 'Product'[Color] )

    VAR VColors =

        VALUES ( 'Product'[Color] )



EVALUATE

ADDCOLUMNS ( TColors, "Amt", [Sales Amount] ) -- Amt shows the same value in every row



EVALUATE

ADDCOLUMNS ( VColors, "Amt", [Sales Amount] )  -- Amt shows a different value in each row 

```

In order to show the results side by side, we need to join the two results into a single table. Again, the different data lineage is an issue. You cannot use [[NATURALINNERJOIN]] with a table and a variable, because the data lineage of the two *Color* columns is different. Certain DAX tricks let you perform the operation anyway, by using [[SELECTCOLUMNS]] to break the lineage before performing the join:

```dax


DEFINE

    TABLE TColors = VALUES ( 'Product'[Color] )

    VAR VColors = VALUES ( 'Product'[Color] )

EVALUATE

NATURALINNERJOIN (

    SELECTCOLUMNS (

        TColors,

        "Color", TColors[Color] & "",

        "Amt from Table", [Sales Amount]

    ),

    SELECTCOLUMNS (

        VColors,

        "Color", 'Product'[Color] & "",

        "Amt from Variable", [Sales Amount]

    )

)

```

The result shows the filtering effect of the table and of the variable. As you can see, the query table does not filter *Sales*, whereas the variable does.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-5.png)

Query tables have their own lineage and you cannot define query relationships. Consequently, query tables have limited usage.

The most relevant detail about query tables is that their semantics is very different than that of variables. A variable stores the result of a query keeping the lineage of the source tables used in the expression. A query table defines its own lineage. Therefore, a variable can filter the model, whereas a query table cannot.

Moreover, query tables are evaluated without any filter context, whereas variables are evaluated in the filter context where they are defined. Consequently, the semantics of variables and query tables are very different. Variable and query tables are definitely not interchangeable.

[[SUMMARIZECOLUMNS]]

Create a summary table for the requested totals over set of groups.

`SUMMARIZECOLUMNS ( [<GroupBy_ColumnName> [, [<FilterTable>] [, [<Name>] [, [<Expression>] [, <GroupBy_ColumnName> [, [<FilterTable>] [, [<Name>] [, [<Expression>] [, … ] ] ] ] ] ] ] ] ] )`

[[NATURALINNERJOIN]]

Joins the Left table with right table using the Inner Join semantics.

`NATURALINNERJOIN ( <LeftTable>, <RightTable> )`

[[SELECTCOLUMNS]]

Returns a table with selected columns from the table and new columns specified by the DAX expressions.

`SELECTCOLUMNS ( <Table> [[, <Name>], <Expression> [[, <Name>], <Expression> [, … ] ] ] )`
