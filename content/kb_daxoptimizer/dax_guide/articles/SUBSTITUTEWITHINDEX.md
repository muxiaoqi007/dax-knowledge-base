---
title: "SUBSTITUTEWITHINDEX"
function: "substitutewithindex"
category: "Table manipulation"
url: "https://dax.guide/substitutewithindex/"
source: "dax.guide"
重要度:
难度:
---

# SUBSTITUTEWITHINDEX DAX Function (Table manipulation)

Returns a table which represents the semijoin of two tables supplied and for which the common set of columns are replaced by a 0-based index column. The index is based on the rows of the second table sorted by specified order expressions.

## Syntax

SUBSTITUTEWITHINDEX ( <Table>, <Name>, <SemiJoinIndexTable>, <Expression> [, [<Order>] [, <Expression> [, [<Order>] [, … ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | Table to be modified. || Name |  | A name of the column to be added to the first table. |
| SemiJoinIndexTable |  | Table that will be ordered and used to calculate index and to join with the first argument. |
| Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression | Repeatable | Order by expression for the second parameter (SemiJoinIndexTable). |
| Order | Optional Repeatable | The order to be applied. 0/FALSE/DESC – descending; 1/TRUE/ASC – ascending. |

## Return values

Table An entire table or a table with one or more columns.

A table that includes only those values present in the SemiJoinIndexTable table (similar to an inner join) and which has an index column instead the value column used to join Table with SemiJoinIndexTable.

## Remarks

The matching between Table and SemiJoinTable follows the same rules used by other join functions, such as [[NATURALINNERJOIN]] and [[NATURALLEFTOUTERJOIN]]: the columns must have the same data lineage, or they should both have no data lineage and the same column name. If a column without data lineage has the same name as a column with data lineage, it is ignored for the join condition. If no columns are matched, the function raises an error.

## Examples

```dax


--  SUBSTITUTEWITHINDEX is a tool function used mainly by

--  Power BI to map values in a query to column in a matrix

--  by substituting the index columns with a number indicating

--  the column number where to put the result.

--  The matching between the two tables is based on data lineage

--  or column names.

DEFINE

    VAR R =

        SUMMARIZECOLUMNS (

            'Product'[Brand],

            'Date'[Calendar Year],

            TREATAS ( { "Contoso", "Fabrikam" }, 'Product'[Brand] ),

            "Amount", [Sales Amount]

        )

    VAR C =

        SUMMARIZE ( Sales, 'Date'[Calendar Year] )

    VAR C_ColumnName =

        SELECTCOLUMNS({"CY 2007", "CY 2008", "CY 2009"}, "Calendar Year", [Value])

    VAR Result =

        SUBSTITUTEWITHINDEX ( R, "Column #", C, [Calendar Year], ASC )



EVALUATE R



EVALUATE C



EVALUATE C_ColumnName



EVALUATE Result



-- The following code would generate an error 

-- because C_ColumnName has the same name

-- of a column with a data lineage

-- EVALUATE

-- SUBSTITUTEWITHINDEX ( R, "Column #", C_ColumnName, [Calendar Year], ASC )



-- The following code works because the 

-- Date[Calendar Year] column loses the data lineage

-- in SELECTCOLUMNS by using an expression

EVALUATE

SUBSTITUTEWITHINDEX ( 

    SELECTCOLUMNS ( 

        R, 

        'Product'[Brand],

        -- the expression remove the data lineage

        "Calendar Year", 'Date'[Calendar Year] & "",

        [Amount]

    ),

    "Column #", C_ColumnName, [Calendar Year], ASC

)



```

| Brand | Calendar Year | Amount |
| --- | --- | --- |
| Contoso | CY 2007 | 2,729,818.54 |
| Fabrikam | CY 2007 | 1,652,751.34 |
| Contoso | CY 2008 | 2,369,167.68 |
| Fabrikam | CY 2008 | 1,993,123.48 |
| Contoso | CY 2009 | 2,253,412.80 |
| Fabrikam | CY 2009 | 1,908,140.91 |

| Calendar Year |
| --- |
| CY 2007 |
| CY 2008 |
| CY 2009 |

| Calendar Year |
| --- |
| CY 2007 |
| CY 2008 |
| CY 2009 |

| Brand | Amount | Column # |
| --- | --- | --- |
| Contoso | 2,729,818.54 | 0 |
| Fabrikam | 1,652,751.34 | 0 |
| Contoso | 2,369,167.68 | 1 |
| Fabrikam | 1,993,123.48 | 1 |
| Contoso | 2,253,412.80 | 2 |
| Fabrikam | 1,908,140.91 | 2 |

| Brand | Amount | Column # |
| --- | --- | --- |
| Contoso | 2,729,818.54 | 0 |
| Fabrikam | 1,652,751.34 | 0 |
| Contoso | 2,369,167.68 | 1 |
| Fabrikam | 1,993,123.48 | 1 |
| Contoso | 2,253,412.80 | 2 |
| Fabrikam | 1,908,140.91 | 2 |

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/substitutewithindex-function-dax](https://docs.microsoft.com/en-us/dax/substitutewithindex-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
