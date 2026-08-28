---
title: "ALLNOBLANKROW"
function: "allnoblankrow"
category: "Filter"
url: "https://dax.guide/allnoblankrow/"
source: "dax.guide"
重要度:
难度:
---

# ALLNOBLANKROW DAX Function (Filter)

Returns all the rows except blank row in a table, or all the values in a column, ignoring any filters that might have been applied.

## Syntax

ALLNOBLANKROW ( <TableNameOrColumnName> [, <ColumnName> [, <ColumnName> [, … ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| TableNameOrColumnName |  | The name of an existing table or column. |
| ColumnName | Optional Repeatable | A column in the same base table. The column can be specified in optional parameters only when a column is used in the first argument, too. |

## Return values

Table An entire table or a table with one or more columns.

The result can include blank values if the table has blank values. The only blank that is not included in the result is the one added to the table in case of invalid relationships.

## Remarks

This function removes the corresponding filters from the filter context. It does not materialize the resulting table when called directly in a filter argument of [[CALCULATE]] or [[CALCULATETABLE]].

The only blank row that is ignored is the one added to a table in case of an invalid relationship. If the table contains blank values in columns, these values are included in the result.

[» 6 related articles](#articles)  
[» 3 related functions](#alt)  

## Examples

The ALLNOBLANKROW function can be applied to either a table or a set of columns.

```dax


ALLNOBLANKROW ( Customer )



ALLNOBLANKROW ( Customer[Country], Customer[State] , Customer[City] )

```

```dax


--  

--  ALLNOBLANKROW still returns blanks, if they are present among the

--  regular rows of the table. The only blank ignored is the one in the

--  blank row

--

EVALUATE

ADDCOLUMNS (

    ALLNOBLANKROW ( Customer[Birth Date] ),

    "# Customers", CALCULATE ( COUNTROWS ( Customer ) )

)

ORDER BY [Birth Date]

```

```dax


--

--  If you need to remove blanks, you need to use either FILTER

--  or CALCULATETABLE to manually remove blanks.

--

EVALUATE

    ADDCOLUMNS (

        FILTER ( 

            ALLNOBLANKROW ( Customer[Birth Date] ), 

            NOT ( Customer[Birth Date] == BLANK () ) 

        ),

        "# Customers", CALCULATE ( COUNTROWS ( Customer ) ) 

    )

ORDER BY [Birth Date]

```

## Related articles

Learn more about ALLNOBLANKROW in the following articles:

- [**Avoiding circular dependency errors in DAX**](https://www.sqlbi.com/articles/avoiding-circular-dependency-errors-in-dax/)

  This article explains how DAX handles dependencies between tables, columns and relationships, to help you avoid circular dependency errors. [» Read more](https://www.sqlbi.com/articles/avoiding-circular-dependency-errors-in-dax/)
- [**Blank row in DAX**](https://www.sqlbi.com/articles/blank-row-in-dax/)

  There are two functions in DAX that return the list of values of a column: VALUES and DISTINCT. This article describes the difference between the two, explaining the details of the blank row added to tables for invalid relationships. [» Read more](https://www.sqlbi.com/articles/blank-row-in-dax/)
- [**Understanding Circular Dependencies in DAX**](https://www.sqlbi.com/articles/understanding-circular-dependencies/)

  This article explains how to avoid circular dependency errors that can occur in DAX when two or more entities (calculated columns or calculated tables) reference one another in such a way that the engine cannot compute their value. [» Read more](https://www.sqlbi.com/articles/understanding-circular-dependencies/)
- [**Preparing a data model for Sankey Charts in Power BI**](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)

  This article describes how to correctly shape a data model and prepare data to use a Sankey Chart as a funnel, considering events related to a customer (contact, trial, subscription, renewal, and others). [» Read more](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)
- [**Understanding the “can’t determine relationship between the fields” error in Power BI**](https://www.sqlbi.com/articles/understanding-the-cant-determine-relationship-between-the-fields-error-in-power-bi/)

  This article explains why you might encounter a curious error when placing columns from unrelated tables in a Power BI matrix. [» Read more](https://www.sqlbi.com/articles/understanding-the-cant-determine-relationship-between-the-fields-error-in-power-bi/)
- [**Creating functions for the like-for-like DAX pattern**](https://www.sqlbi.com/articles/creating-functions-for-the-like-for-like-dax-pattern/)

  This article offers a comprehensive guide to changing the like-for-like pattern into model-independent functions to enhance flexibility and simplify DAX code. [» Read more](https://www.sqlbi.com/articles/creating-functions-for-the-like-for-like-dax-pattern/)

## Related functions

Other related functions are:

- [[ALL]]
- [[ALLEXCEPT]]
- [[ALLSELECTED]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/allnoblankrow-function-dax](https://docs.microsoft.com/en-us/dax/allnoblankrow-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
