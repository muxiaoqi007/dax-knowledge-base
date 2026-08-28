---
title: "VALUES"
function: "values"
category: "Table manipulation"
url: "https://dax.guide/values/"
source: "dax.guide"
重要度:
难度:
---

# VALUES DAX Function (Table manipulation)

When a column name is given, returns a single-column table of unique values. When a table name is given, returns a table with the same columns and all the rows of the table (including duplicates) with the [additional blank row](https://www.sqlbi.com/articles/blank-row-in-dax/) caused by an invalid relationship if present.

## Syntax

VALUES ( <TableNameOrColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| TableNameOrColumnName |  | A column name or a table name. |

## Return values

Table An entire table or a table with one or more columns.

A column of unique values if the parameter is a single column.  
If the parameter is a table expression, the result has the same columns and does not remove duplicated rows.

## Remarks

VALUES is similar to [[DISTINCT]], but it can have [an additional blank row](https://www.sqlbi.com/articles/blank-row-in-dax/) in case the table has at least one one-to-many relationship with other tables where there is a violation of referential integrity.

[» 9 related articles](#articles)  
[» 1 related function](#alt)  

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

Learn more about VALUES in the following articles:

- [**Physical and Virtual Relationships in DAX**](https://www.sqlbi.com/articles/physical-and-virtual-relationships-in-dax/)

  DAX calculations can leverage relationships present in the data model, but you can obtain the same result without physical relationships, applying equivalent filters using specific DAX patterns. This article show a more efficient technique to apply virtual relationships in DAX… [» Read more](https://www.sqlbi.com/articles/physical-and-virtual-relationships-in-dax/)
- [**Using ALLEXCEPT versus ALL and VALUES**](https://www.sqlbi.com/articles/using-allexcept-versus-all-and-values/)

  This article describes the semantic difference between ALLEXCEPT and the joint use of ALL and VALUES, showing practical examples of the different results in Power BI and SSAS 2016. [» Read more](https://www.sqlbi.com/articles/using-allexcept-versus-all-and-values/)
- [**Blank row in DAX**](https://www.sqlbi.com/articles/blank-row-in-dax/)

  There are two functions in DAX that return the list of values of a column: VALUES and DISTINCT. This article describes the difference between the two, explaining the details of the blank row added to tables for invalid relationships. [» Read more](https://www.sqlbi.com/articles/blank-row-in-dax/)
- [**Using SELECTEDVALUE with Fields Parameters in Power BI**](https://www.sqlbi.com/blog/marco/2022/06/11/using-selectedvalue-with-fields-parameters-in-power-bi/)

  If you try to use SELECTEDVALUE on the visible column of the table generated by the Fields Parameters feature in Power BI, you get the following error: Calculation error in measure ‘Sales'[Selection]: Column [Parameter] is part of composite key, but… [» Read more](https://www.sqlbi.com/blog/marco/2022/06/11/using-selectedvalue-with-fields-parameters-in-power-bi/)
- [**Understanding blank row and limited relationships**](https://www.sqlbi.com/articles/understanding-blank-row-and-limited-relationships/)

  DAX creates a blank row to guarantee that results are accurate even if a regular relationship is invalid. The blank row is not created for limited relationships. This article shows the effect of not having a blank row in your tables. [» Read more](https://www.sqlbi.com/articles/understanding-blank-row-and-limited-relationships/)
- [**Blank in date columns and DAX time intelligence functions**](https://www.sqlbi.com/articles/blank-in-date-columns-and-dax-time-intelligence-functions/)

  This article explores the implications of having blank values in date columns and provides the best practices for managing them in DAX calculations and Power BI reports. [» Read more](https://www.sqlbi.com/articles/blank-in-date-columns-and-dax-time-intelligence-functions/)
- [**Choosing between DISTINCT and VALUES in DAX**](https://www.sqlbi.com/articles/choosing-between-distinct-and-values-in-dax/)

  When should you use DISTINCT over VALUES in DAX? Here is how to write resilient measures that survive bad data and model changes. [» Read more](https://www.sqlbi.com/articles/choosing-between-distinct-and-values-in-dax/)
- [**Using VALUES in iterators**](https://www.sqlbi.com/articles/using-values-in-iterators/)

  Why and when you should use VALUES while iterating a table reference in DAX. [» Read more](https://www.sqlbi.com/articles/using-values-in-iterators/)
- [**Using VALUES in SUMMARIZE**](https://www.sqlbi.com/articles/using-values-in-summarize/)

  This article describes when to use VALUES in a table grouped by SUMMARIZE, then goes on to explain why you cannot however use VALUES with SUMMARIZECOLUMNS. [» Read more](https://www.sqlbi.com/articles/using-values-in-summarize/)

## Related functions

Other related functions are:

- [[DISTINCT]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/values-function-dax](https://docs.microsoft.com/en-us/dax/values-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
