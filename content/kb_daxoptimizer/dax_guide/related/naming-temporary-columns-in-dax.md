---
title: "Naming temporary columns in DAX"
url: "https://www.sqlbi.com/articles/naming-temporary-columns-in-dax"
published: "2020-08-17"
updated: 
source: "sqlbi.com"
---

# Naming temporary columns in DAX

> 发布：2020-08-17

Table functions in DAX expressions can create temporary columns that are not tied to any column in the model. Temporary columns do not filter any column in the filter context unless you use [[TREATAS]] (see [data lineage](https://www.sqlbi.com/articles/understanding-data-lineage-in-dax/) described in a previous article) so a standard convention is of great help in order to differentiate between model columns and temporary columns.

Temporary columns often appear in DAX code because the DAX formula needs to create temporary tables. For example, the following expression computes the sales amount for strictly the products that sold more than a given amount:

```dax


FilteredSalesAmount =

VAR AmountLimit = 100000

VAR SalesByProduct =

    ADDCOLUMNS (

        'Product',

        "ProdSalesAmt", [Sales Amount]

    )

VAR BestProducts =

    FILTER (

        SalesByProduct,

        [ProdSalesAmt] >= AmountLimit

    )

VAR SalesAmountOfBest =

    SUMX (

        BestProducts,

        [ProdSalesAmt]

    )

RETURN

    SalesAmountOfBest

```

The formula works just well, but it violates one of the golden rules of DAX code: you always prefix a column reference with its table name, and you never use the table name when referencing a measure. Therefore, when reading DAX code, *[Sales Amt]* is a measure reference, whereas *‘Product'[Sales Amt]* is a column reference.

Nevertheless, in our DAX example *ProdSalesAmt* is a column of a temporary table (*SalesByProduct*) created by the *FilteredSalesAmount* measure. As such, *ProdSalesAmt* is a temporary column that does not originate from any column in the model and does not have a table name you can use as a prefix. This situation creates ambiguity in the code: it is not easy to discriminate between a column reference and a measure reference. Therefore, the code is harder to read and more prone to containing errors.

Beware that the *BestProducts* table variable contains two types of columns: model columns like *‘Product'[Category]*, and temporary columns like *[ProdSalesAmt]*. You can fully qualify model columns using the table name and remove any ambiguity, but you cannot qualify temporary columns using a table name. Indeed, temporary columns do not have data lineage and do not filter model columns. Even though in the simple example the ambiguity is quickly solved by looking at the whole formula, there are more complex scenarios where the expression is so long that it is hard to get an overall look at the code.

It would be just great if DAX had a feature to provide aliases to tables, like SQL does – but this feature is not available in DAX. Therefore, we suggest adopting the following standard in DAX code:

**Temporary column names should always start with the @special character.**

The previous expression can thus be rewritten as follows:

```dax


FilteredSalesAmount =

VAR AmountLimit = 100000

VAR SalesByProduct =

    ADDCOLUMNS (

        'Product',

        "@ProdSalesAmt", [Sales Amount]

    )

VAR BestProducts =

    FILTER (

        SalesByProduct,

        [@ProdSalesAmt] >= AmountLimit

    )

VAR SalesAmountOfBest =

    SUMX (

        BestProducts,

        [@ProdSalesAmt]

    )

RETURN

    SalesAmountOfBest

```

We opted for this standard for several reasons:

- It is very unlikely that a model measure name starts with @. If that were the case… well, you could just rename the measure to avoid any ambiguity.
- The @ symbol does not visually disturb the reading of the column name.
- We just liked it much more than any other alternative we considered, and we discussed this internally to death before deciding; after all, DAX is our bread and butter.

Therefore, we suggest adopting the following rules while naming column in temporary tables:

- If a column has the data lineage of a model column, use the fully qualified *‘table'[column]*
- If a column is temporary, then always prefix its name with the @ symbol.
- However, if a column is the result of a query, you should avoid using the @ symbol at all.

The last rule is an exception to avoid propagating the @ character outside of the internal use of a DAX expression. For example, in the following query *ProdSales* is a temporary column:

```dax


EVALUATE

SUMMARIZECOLUMNS ( 

    'Product'[Category],

    "ProdSales", [Sales Amount]

)

```

Nevertheless, if we used the @ symbol to prefix the *ProdSales* name, the resulting dataset would contain the @ symbol in the column name, making it harder to consume. Besides, the relevance of the rule is to be able to discriminate between temporary columns and model columns (columns with a data lineage) in DAX code. The result of a query is not used by any further DAX expression. Instead, it is consumed by the caller in a report or in a calculated table. As such, it is a good practice that of using names that are clear in the user interface, deliberately violating the rule we outlined here. Rules serve the purpose of making the code easier, so exceptions are acceptable in order to achieve this ultimate goal.

As a final example, take a look at a more complex DAX expression written using this standard. As you can appreciate in all the highlighted rows, it is easy to recognize [@Sales] as a column reference instead of a measure reference, even when the column is referenced far from its definition:

```dax


ABC Sales Amount =

VAR SalesByProduct =

    ADDCOLUMNS (

        ALLSELECTED ( 'Product' ),

        "@Sales", [Sales Amount]

    )

VAR AllSalesAmount =

    CALCULATE (

        [Sales Amount],

        ALLSELECTED ( 'Product' )

    )

VAR SalesAndPercByProduct =

    ADDCOLUMNS (

        SalesByProduct,

        "@AggSales%",

        VAR CurrentSalesAmt = [@Sales]

        VAR CumulatedSalesAmt =

            SUMX (

                FILTER (

                    SalesByProduct,

                    [@Sales] >= CurrentSalesAmt

                ),

                [@Sales]

            )

        RETURN

            DIVIDE (

                CumulatedSalesAmt,

                AllSalesAmount

            )

    )

VAR ProductsInClass =

    FILTER (

        CROSSJOIN (

            SalesAndPercByProduct,

            'ABC Classes'

        ),

        AND (

            [@AggSales%] > 'ABC Classes'[Lower Boundary],

            [@AggSales%] <= 'ABC Classes'[Upper Boundary]

        )

    )

VAR Result =

    CALCULATE (

        [Sales Amount],

        KEEPFILTERS ( ProductsInClass )

    )

RETURN

    Result

```

We at SQLBI have adopted this standard and you will see it in all future articles and books.

Let us know your thoughts by commenting this article and have fun with DAX!

[[TREATAS]]

Treats the columns of the input table as columns from other tables. For each column, filters out any values that are not present in its respective output column.

`TREATAS ( <Expression>, <ColumnName> [, <ColumnName> [, … ] ] )`
