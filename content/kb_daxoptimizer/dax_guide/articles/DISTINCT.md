---
title: "DISTINCT"
function: "distinct"
category: "Table manipulation"
url: "https://dax.guide/distinct/"
source: "dax.guide"
重要度:
难度:
---

# DISTINCT DAX Function (Table manipulation)

Returns a one column table that contains the distinct (unique) values in a column, for a column argument. Or multiple columns with distinct (unique) combination of values, for a table expression argument.

## Syntax

DISTINCT ( <ColumnNameOrTableExpr> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnNameOrTableExpr |  | The column (or table expression) from which unique values (or combination of values) are to be returned. |

## Return values

Table An entire table or a table with one or more columns.

A column of unique values if the parameter is a single column. If the parameter is a table expression, the result has the same columns and remove only duplicated rows.

## Remarks

If the parameter is a single column, the result of DISTINCT is affected by the current filter context. In this case it is important to understand the differences with [[VALUES]], which might add [an additional blank row](https://www.sqlbi.com/articles/blank-row-in-dax/) in case of invalid relationships.  
If the parameter is a table expression, DISTINCT returns a table by removing duplicate rows provided by the table expression.

[» 7 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  DISTINCT retrieves the distinct values of a column

--  VALUES does the same.

--  They differ in the way they handle the blank row generated

--  for invalid relationships, if present

--  DISTINCT does not return the blank row caused by an invalid relationship 

--  VALUES includes the blank row caused by an invalid relationship, if present

EVALUATE

SUMMARIZECOLUMNS (

    Store[Continent],

    "#Stores (no blank row)", COUNTROWS ( DISTINCT ( Store[Store Name] ) ),

    "#Stores (blank row)",    COUNTROWS ( VALUES ( Store[Store Name] ) )

)

```

| Continent | #Stores (no blank row) | #Stores (blank row) |
| --- | --- | --- |
| North America | 209 | 209 |
| Europe | 54 | 54 |
| Asia | 41 | 41 |
| (Blank) | (Blank) | 1 |

```dax


--  DISTINCT and VALUES can also be used with a table.

--  DISTINCT returns the distinct rows in the table.

--  VALUES returns the table, with the blank row caused 

--  by an invalid relationship if it exists,

--  but without performing a distinct.

--  Worth remembering that a table reference does not return

--  the blank row caused by an invalid relationship if you 

--  just use the table name.

EVALUATE

SUMMARIZECOLUMNS (

    Store[Continent],

    "#Stores",                COUNTROWS ( Store ),

    "#Stores (no blank row)", COUNTROWS ( DISTINCT ( Store ) ),

    "#Stores (blank row)",    COUNTROWS ( VALUES ( Store ) )

)



```

| Continent | #Stores | #Stores (no blank row) | #Stores (blank row) |
| --- | --- | --- | --- |
| North America | 209 | 209 | 209 |
| Europe | 54 | 54 | 54 |
| Asia | 41 | 41 | 41 |
| (Blank) | (Blank) | (Blank) | 1 |

```dax


--  DISTINCT can be used with variables.

--  VALUES requires a table or a column that exists in the model.

EVALUATE

VAR Stores = 

    SELECTCOLUMNS ( Store, "Continent", Store[Continent] )

RETURN

    {

        ( "#Stores (all rows)",     COUNTROWS (          ( Stores ) ) ),

        ( "#Stores (no blank row)", COUNTROWS ( DISTINCT ( Stores ) ) )

        --

        --  The following would produce an error: VALUES cannot be used with variables

        --

        -- ( "#Stores (blank row)",    COUNTROWS ( VALUES ( Stores ) ) )

    }

```

| Value1 | Value2 |
| --- | --- |
| #Stores (all rows) | 304 |
| #Stores (no blank row) | 3 |

## Related articles

Learn more about DISTINCT in the following articles:

- [**Difference between DISTINCT and VALUES in DAX**](https://www.sqlbi.com/blog/marco/2011/03/08/difference-between-distinct-and-values-in-dax/)

  This short post describes the differences between DISTINCT and VALUES. [» Read more](https://www.sqlbi.com/blog/marco/2011/03/08/difference-between-distinct-and-values-in-dax/)
- [**Blank row in DAX**](https://www.sqlbi.com/articles/blank-row-in-dax/)

  There are two functions in DAX that return the list of values of a column: VALUES and DISTINCT. This article describes the difference between the two, explaining the details of the blank row added to tables for invalid relationships. [» Read more](https://www.sqlbi.com/articles/blank-row-in-dax/)
- [**Analyzing the performance of DISTINCTCOUNT in DAX**](https://www.sqlbi.com/articles/analyzing-distinctcount-performance-in-dax/)

  This article describes how to analyze the performance of a DAX measure based on a DISTINCTCOUNT calculation and how to evaluate possible optimizations. [» Read more](https://www.sqlbi.com/articles/analyzing-distinctcount-performance-in-dax/)
- [**From SQL to DAX: Projection**](https://www.sqlbi.com/articles/from-sql-to-dax-projection/)

  This article describes projection functions and techniques in DAX, showing the differences between SELECTCOLUMNS, ADDCOLUMNS, and SUMMARIZE. [» Read more](https://www.sqlbi.com/articles/from-sql-to-dax-projection/)
- [**Understanding blank row and limited relationships**](https://www.sqlbi.com/articles/understanding-blank-row-and-limited-relationships/)

  DAX creates a blank row to guarantee that results are accurate even if a regular relationship is invalid. The blank row is not created for limited relationships. This article shows the effect of not having a blank row in your tables. [» Read more](https://www.sqlbi.com/articles/understanding-blank-row-and-limited-relationships/)
- [**Choosing between DISTINCT and VALUES in DAX**](https://www.sqlbi.com/articles/choosing-between-distinct-and-values-in-dax/)

  When should you use DISTINCT over VALUES in DAX? Here is how to write resilient measures that survive bad data and model changes. [» Read more](https://www.sqlbi.com/articles/choosing-between-distinct-and-values-in-dax/)
- [**Using VALUES in iterators**](https://www.sqlbi.com/articles/using-values-in-iterators/)

  Why and when you should use VALUES while iterating a table reference in DAX. [» Read more](https://www.sqlbi.com/articles/using-values-in-iterators/)

## Related functions

Other related functions are:

- [[DISTINCTCOUNT]]
- [[VALUES]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/distinct-function-dax](https://docs.microsoft.com/en-us/dax/distinct-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
