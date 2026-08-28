---
title: "Table and column references using DAX variables"
url: "https://www.sqlbi.com/articles/table-and-column-references-using-dax-variables"
published: "2020-08-17"
updated: 
source: "sqlbi.com"
---

# Table and column references using DAX variables

> 发布：2020-08-17

Using variables in DAX makes the code much easier to write and read. You can split a complex operation into smaller steps by storing a number, a string, or a table into a variable. When you store a scalar value in a variable, the behavior is intuitive and common to many other languages.

For example, you can write a measure computing the margin percentage this way:

```dax


Margin % := 

VAR Revenues = [Sales Amount]

VAR Cost = SUMX ( Sales, Sales[Quantity] * Sales[Unit Cost] )

VAR Margin = Revenues - Cost

VAR MarginPerc = DIVIDE ( Margin, Revenues )

RETURN MarginPerc

```

The variables Revenues, Cost, Margin and MarginPerc contain numbers that are used in subsequent DAX expressions after each variable definition. Every value is read by simply referencing the variable name.

A variable can also store a table, which can be used as a filter argument in [[CALCULATE]]. For example, the following measure computes the sales amount of the top 10 products in any given selection of the report – such as the top 10 products of a color or of a category, depending on the report selection:

```dax


Sales Top 10 Products := 

VAR Top10Products = 

    TOPN ( 

        10, 

        'Product', 

        [Sales Amount] 

    )

VAR Result =

    CALCULATE (

        [Sales Amount],

        Top10Products

    )

RETURN Result

```

The Top10Products variable is like a temporary table that contains all the columns of the Product table. The entire result of [[TOPN]] is used as an argument of the following [[CALCULATE]], so we do not have to think about how each column of the table in Top10Products could be accessible.

![](https://cdn.sqlbi.com/wp-content/uploads/Table-and-column-references-using-DAX-variables-01.png)

But what if we want to create a measure that lists the top three products of the current selection? Without using variables, the measure can be as follows:

```dax


Top 3 Products no var := 

CONCATENATEX (

    TOPN (

        3,

        'Product',

        [Sales Amount],

        DESC

    ),

    'Product'[Product Name],

    ", ",

    [Sales Amount]

)

```

You may notice that the Sales Amount measure is evaluated twice for the top three products found. Indeed, the Sales Amount value computed in [[TOPN]] is not persisted in the result of [[TOPN]], which only contains columns of the Product table. We will see how to optimize this later. For now, just focus on how [[CONCATENATEX]] uses the result provided by [[TOPN]]: the Product Name column reference uses Product as a table name. This seems intuitive because [[TOPN]] returns a result which is just a filtered set of rows of the Product table. However, what happens if we assign the result of [[TOPN]] to a variable? The code is not much different:

```dax


Top 3 Products var := 

VAR Top3Products = 

    TOPN (

        3,

        'Product',

        [Sales Amount],

        DESC

    )

VAR Result =

    CONCATENATEX (

        Top3Products,

        'Product'[Product Name],

        ", ",

        [Sales Amount],

        ASC

    )

RETURN 

    Result

```

However, a developer might make the wrong assumption that assigning a table to a variable transforms the variable into a table, with its own columns and a new set of column references. This is not the case. Indeed, you cannot write the following code:

```dax


Top 3 Products not valid := 

VAR Top3Products = 

    TOPN (

        3,

        'Product',

        [Sales Amount],

        DESC

    )

VAR Result =

    CONCATENATEX (

        Top3Products,

        Top3Products[Product Name], -- Invalid code

        ", ",

        [Sales Amount],

        ASC

    )

RETURN 

    Result

```

This code generates the DAX error, “Cannot find table Top3Products”. A column reference must always reference an existing column of the data model, or a column that has been generated using a table function assigning a specific name to it.

Thus, a variable name cannot be used as a table name in a column reference. However, what happens with new columns created within a DAX expression? We can see a useful example trying to avoid the double calculation of Sales Amount by the [[CONCATENATEX]] function, which needs to compute Sales Amount to establish the display order of the products:

```dax


Top 3 Products var 2 = 

VAR ProductsSales =

    ADDCOLUMNS (

        'Product',

        "Sales", [Sales Amount]

    )

VAR Top3Products = 

    TOPN (

        3,

        ProductsSales,

        [Sales],        -- This is a column reference, not a measure reference

        DESC

    )

VAR Result =

    CONCATENATEX (

        Top3Products,

        'Product'[Product Name],

        ", ",

        [Sales],        -- This is a column reference, not a measure reference

        ASC

    )

RETURN 

    Result

```

The ProductsSales variable contains a table with all the columns of Product, plus an additional column (Sales) with the result of the Sales Amount measure computed for each product. This way, the syntax [Sales] is just a column reference, not a measure reference. Indeed, there is no measure named Sales in the model. Once again, the following syntax would be invalid, because ProductsSales is a variable and not a table:

```dax


...

VAR Top3Products = 

    TOPN (

        3,

        ProductsSales,

        ProductsSales[Sales],        -- This is a column reference, not a measure reference

        DESC

    )

...

```

However, there are two options if you want to use an explicit column reference to make the code easier to read. These two options fully respect the following two important rules for [DAX code formatting](https://www.sqlbi.com/articles/rules-for-dax-code-formatting/):

- Always use table names for column references
- Never use table names for measures

The first option is to use the empty table name in the column reference. The following measure is valid:

```dax


Top 3 Products var 3 := 

VAR ProductsSales =

    ADDCOLUMNS (

        'Product',

        "Sales", [Sales Amount]   -- Valid column reference

    )

VAR Top3Products = 

    TOPN (

        3,

        ProductsSales,

        ''[Sales],

        DESC

    )

VAR Result =

    CONCATENATEX (

        Top3Products,

        'Product'[Product Name],

        ", ",

        ''[Sales],   -- Valid column reference

        ASC

    )

RETURN 

    Result

```

The current version of Power BI Desktop (April 2019) marks the two column references ”[Sales] as an IntelliSense error, but this is a valid DAX syntax and the measure works without any issue. Yes, this is a bug in IntelliSense!

The second technique that can be used to fully respect the best practice for column references is to name the column including a table name in the [[ADDCOLUMNS]] function. You can use either existing names or new names, including the name of a variable!

```dax


Top 3 Products var 4 := 

VAR ProductsSales =

    ADDCOLUMNS (

        'Product',

        "ProductSales[Sales]", [Sales Amount]

    )

VAR Top3Products = 

    TOPN (

        3,

        ProductsSales,

        ProductSales[Sales],    -- Column reference

        DESC

    )

VAR Result =

    CONCATENATEX (

        Top3Products,

        'Product'[Product Name],

        ", ",

        ProductSales[Sales],   -- Column reference

        ASC

    )

RETURN 

    Result

```

Is it necessary to use one of these techniques? Usually, when the new column name is unique and the DAX expression is simple enough, we can live with an exception to the best practice for column references. But in case you have a complex model and a complex measure, you may consider using the latter technique – also making it clear that the table name is that of a variable using one technique described in the [Naming Variables in DAX](https://www.sqlbi.com/blog/marco/2019/01/15/naming-variables-in-dax/) blog post, such as a double underscore prefix for variable names:

```dax


Top 3 Products var 5 := 

VAR __ProductsSales =

    ADDCOLUMNS (

        'Product',

        "__ProductSales[Sales]", [Sales Amount]

    )

VAR __Top3Products = 

    TOPN (

        3,

        __ProductsSales,

        __ProductSales[Sales],    -- Column reference

        DESC

    )

VAR __Result =

    CONCATENATEX (

        __Top3Products,

        'Product'[Product Name],

        ", ",

        __ProductSales[Sales],   -- Column reference

        ASC

    )

RETURN 

    __Result



```

Using the variable name as a table name for new columns created by [[ADDCOLUMNS]], [[SELECTCOLUMNS]] or other similar DAX functions can be a good idea to make the code simpler to read in a very long and complex DAX expression. We do not however think that is necessary in simple measures like the ones described in this article!

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[TOPN]]

Returns a given number of top rows according to a specified expression.

`TOPN ( <N_Value>, <Table> [, <OrderBy_Expression> [, [<Order>] [, <OrderBy_Expression> [, [<Order>] [, … ] ] ] ] ] )`

[[CONCATENATEX]]

Evaluates expression for each row on the table, then return the concatenation of those values in a single string result, seperated by the specified delimiter.

`CONCATENATEX ( <Table>, <Expression> [, <Delimiter>] [, <OrderBy_Expression> [, [<Order>] [, <OrderBy_Expression> [, [<Order>] [, … ] ] ] ] ] )`

[[ADDCOLUMNS]]

Returns a table with new columns specified by the DAX expressions.

`ADDCOLUMNS ( <Table>, <Name>, <Expression> [, <Name>, <Expression> [, … ] ] )`

[[SELECTCOLUMNS]]

Returns a table with selected columns from the table and new columns specified by the DAX expressions.

`SELECTCOLUMNS ( <Table> [[, <Name>], <Expression> [[, <Name>], <Expression> [, … ] ] ] )`
