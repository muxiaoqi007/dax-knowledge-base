---
title: "Check Empty Table Condition with DAX"
url: "https://www.sqlbi.com/articles/check-empty-table-condition-with-dax"
published: "2020-08-17"
updated: 
source: "sqlbi.com"
---

# Check Empty Table Condition with DAX

> 发布：2020-08-17

The simplest approach is using [[COUNTROWS]] and testing whether the result is equal to zero.

```dax
COUNTROWS (

    CALCULATETABLE (

        'Internet Sales',

        'Product Category'[Product Category Name] = "Bikes"

    )

) = 0
```

The [[CALCULATETABLE]] expression in the previous example can be any table expression. Usually you need to test the result of a particular filter and you use this test in the logical expression in an [[IF]] statement.

In reality, when a table is empty, the [[COUNTROWS]] function returns [[BLANK]]. Such a value is automatically converted to 0 in a scalar expression, so in a logical expression [[BLANK]] is equal to 0. However, with this knowledge in mind, we can rewrite the previous expression in this way:

```dax
ISBLANK (

    COUNTROWS (

        CALCULATETABLE (

            'Internet Sales',

            'Product Category'[Product Category Name] = "Bikes"

        )

    )

)
```

I think that the syntax using [[ISBLANK]] is more readable, and it could also produce some benefit in the query plan. However, when the table is not empty, you are always counting all the rows of the table, even if this is not strictly required. Microsoft added a DAX function in SQL Server 2012 SP1 Cumulative Update 4, so any build of Analysis Services 2012 and Power Pivot for Excel 2010 higher than 11.00.3368 can use the [[ISEMPTY]] syntax:

```dax
ISEMPTY (

    CALCULATETABLE (

        'Internet Sales',

        'Product Category'[Product Category Name]

    )

)
```

This syntax is simpler, meaningful and also faster. If you use DAXMD (query DAX on a Multidimensional model) the performance improvement offered by [[ISEMPTY]] is very important, and you should avoid using any [[COUNTROWS]] version to check if a table is empty.

Unfortunately, [[ISEMPTY]] is not available in Excel 2013, so using it in an Excel 2010 data model in Power Pivot will break the calculation once you upgrade the workbook to Excel 2013.

### Best Practice in Testing Empty Table in DAX

This is the order of preference for testing an empty table in DAX:

1. **[[ISEMPTY]]** ( <table expression> )
2. **[[ISBLANK]]** ( [[COUNTROWS]] ( <table expression> ) )
3. **[[COUNTROWS]]** ( <table expression> ) = 0

Remember that [[ISEMPTY]] is available only in SQL Server 2012 SP1 CU4 (build 11.00.3368) or later versions. At the moment of writing, it is still not available in Excel 2013 (I hope it will be introduced in a future update of Excel).

[[COUNTROWS]]

Counts the number of rows in a table.

`COUNTROWS ( [<Table>] )`

[[CALCULATETABLE]]

Context transition

Evaluates a table expression in a context modified by filters.

`CALCULATETABLE ( <Table> [, <Filter> [, <Filter> [, … ] ] ] )`

[[IF]]

Checks whether a condition is met, and returns one value if TRUE, and another value if FALSE.

`IF ( <LogicalTest>, <ResultIfTrue> [, <ResultIfFalse>] )`

[[BLANK]]

Returns a blank.

`BLANK ( )`

[[ISBLANK]]

Checks whether a value is blank, and returns TRUE or FALSE.

`ISBLANK ( <Value> )`

[[ISEMPTY]]

Returns true if the specified table or table-expression is Empty.

`ISEMPTY ( <Table> )`
