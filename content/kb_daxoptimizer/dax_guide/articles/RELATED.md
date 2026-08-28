---
title: "RELATED"
function: "related"
category: "Relationship management"
url: "https://dax.guide/related/"
source: "dax.guide"
重要度:
难度:
---

# RELATED DAX Function (Relationship management)

Returns a related value from another table.

## Syntax

RELATED ( <ColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName |  | The column that contains the desired value. |

## Return values

Scalar A single value of any type.

A single value that is related to the current row.

## Remarks

The RELATED function requires that a regular relationship exists between the current table and the table with related information. The argument specifies a column reference, and the function follows a chain of one or more many-to-one relationships to fetch the value from the specified column in the related table. If a relationship does not exist, RELATED raises an error. The RELATED function cannot be used to fetch a column across a limited relationship.

The RELATED function needs a row context; therefore, it can only be used in calculated column expression, where the current row context is unambiguous, or as a nested function in an expression that uses a table scanning function. A table scanning function, such as [[SUMX]], gets the value of the current row value and then scans another table for instances of that value.

[» 5 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  RELATED is needed to access columns of the expanded table

DEFINE

    MEASURE Sales[Sales Amount] =

        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

    MEASURE Sales[Sales at List Price] =

        SUMX ( Sales, Sales[Quantity] * RELATED ( 'Product'[List Price] ) )

EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Calendar Year],

    "Sales Amount", [Sales Amount],

    "Sales at List Price", [Sales at List Price]

)

```

| Calendar Year | Sales Amount | Sales at List Price |
| --- | --- | --- |
| 2007-01-01 | 11,309,946.12 | 12,457,410.85 |
| 2008-01-01 | 9,927,582.99 | 11,031,426.30 |
| 2009-01-01 | 9,353,814.87 | 10,201,311.36 |

## Related articles

Learn more about RELATED in the following articles:

- [**Row Context and Filter Context in DAX**](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)

  Understanding the difference between row context and filter context is important in using DAX correctly. This article introduces these two concepts. [» Read more](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)
- [**Lookup multiple values in DAX**](https://www.sqlbi.com/articles/lookup-multiple-values-in-dax/)

  This article describes different techniques to retrieve multiple values from a lookup table in DAX, improving code readability and performance. [» Read more](https://www.sqlbi.com/articles/lookup-multiple-values-in-dax/)
- [**Using join functions in DAX**](https://www.sqlbi.com/articles/using-join-functions-in-dax/)

  This article describes the practical uses of NATURALLEFTOUTERJOIN and NATURALINNERJOIN in DAX. These functions are not commonly used in DAX because they do not have the same flexibility as the corresponding concepts in SQL. [» Read more](https://www.sqlbi.com/articles/using-join-functions-in-dax/)
- [**Using RELATED and RELATEDTABLE in DAX**](https://www.sqlbi.com/articles/using-related-and-relatedtable-in-dax/)

  RELATED and its companion function RELATEDTABLE, are two common DAX functions that are required when using a row context with relationships. In this article we describe why and when to use these two functions. [» Read more](https://www.sqlbi.com/articles/using-related-and-relatedtable-in-dax/)
- [**Understanding the interactions between composite models and calculation groups**](https://www.sqlbi.com/articles/understanding-the-interactions-between-composite-models-and-calculation-groups/)

  When used in a composite model, calculation groups show a very unique behavior that a good DAX developer must understand well to build sound models. In this article we describe how composite models and calculation groups work together. [» Read more](https://www.sqlbi.com/articles/understanding-the-interactions-between-composite-models-and-calculation-groups/)

## Related functions

Other related functions are:

- [[RELATEDTABLE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/related-function-dax](https://docs.microsoft.com/en-us/dax/related-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
