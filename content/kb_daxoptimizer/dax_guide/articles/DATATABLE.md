---
title: "DATATABLE"
function: "datatable"
category: "Table manipulation"
url: "https://dax.guide/datatable/"
source: "dax.guide"
重要度:
难度:
---

# DATATABLE DAX Function (Table manipulation)

Returns a table with data defined inline.

## Syntax

DATATABLE ( <Name>, <DataType> [, <Name>, <DataType> [, … ] ], <Data> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Name | Repeatable | A column name to be defined. |
| DataType | Repeatable | A type name to be associated with the column: BOOLEAN/LOGICAL, CURRENCY/DECIMAL, DATETIME, DOUBLE, INTEGER/INT64, STRING/TEXT. |
| Data |  | The data for the table. |

## Return values

Table An entire table or a table with one or more columns.

A table declaring an inline set of values.

## Remarks

Unlike DATATABLE, the [table constructor](https://dax.guide/op/table-constructor/) allows any scalar expressions as input values.

The syntax used by DATATABLE is different from that used by the [table constructor](https://dax.guide/op/table-constructor/).

The data type name specified in DAX differs from the data types available in the user interface of products that use DAX, such as Power BI, Excel, and Visual Studio. Specifically, [[CURRENCY]] corresponds to a Fixed Decimal Number in Power BI, whereas DOUBLE corresponds to the Decimal Number in Power BI and Decimal in the [DAX Data types](https://dax.guide/datatypes/) list.

[» 4 related articles](#articles)  

## Examples

```dax


--  DATATABLE is useful to build constant tables in code.

--  It requires the list of arguments and the list of rows

--  to build the table.

EVALUATE

DATATABLE (

    "Name", STRING,

    "Ordinal", INTEGER,

    {

        { "Small",  1 },

        { "Medium", 2 },

        { "Large",  3 }

    }

)

ORDER BY [Ordinal]

```

| Name | Ordinal |
| --- | --- |
| Small | 1 |
| Medium | 2 |
| Large | 3 |

Values in the definition of the table cannot be expressions; they need to be constant. The following syntax is not valid and generates an error.

```dax


EVALUATE

DATATABLE (

    "Aggregation", STRING,

    "Value", CURRENCY,

    {

        { "Min", MIN ( Sales[Net Price] ) },

        { "Max", MAX ( Sales[Net Price] ) }

    }

)

```

Tables with calculated expressions can be computed using the ROW function, or the table constructor {}, instead of using DATATABLE.  
The table constructor requires renaming the columns.

```dax


EVALUATE

    UNION (

        ROW ( "Aggregation", "Min", "Value", MIN ( Sales[Net Price] ) ),

        ROW ( "Aggregation", "Max", "Value", MAX ( Sales[Net Price] ) )

    )



EVALUATE

    SELECTCOLUMNS ( 

        {

            ( "Min", MIN ( Sales[Net Price] ) ),

            ( "Max", MAX ( Sales[Net Price] ) )

        },

        "Aggregation", [Value1],

        "Value", [Value2] 

    )

```

| Aggregation | Value |
| --- | --- |
| Min | 0.76 |
| Max | 3,199.99 |

| Aggregation | Value |
| --- | --- |
| Min | 0.76 |
| Max | 3,199.99 |

## Related articles

Learn more about DATATABLE in the following articles:

- [**Create Static Tables in DAX Using the DATATABLE Function**](https://www.sqlbi.com/articles/create-static-tables-in-dax-using-the-datatable-function/)

  You can create static tables in DAX using the DATATABLE function. This article describes the syntax of this new feature and shows when and how to use it. [» Read more](https://www.sqlbi.com/articles/create-static-tables-in-dax-using-the-datatable-function/)
- [**Understanding data lineage in DAX**](https://www.sqlbi.com/articles/understanding-data-lineage-in-dax/)

  Data lineage is such a well-implemented DAX feature that most developers use it without knowing about it. This article describes data lineage and how it can help in producing better DAX code. [» Read more](https://www.sqlbi.com/articles/understanding-data-lineage-in-dax/)
- [**Create static tables in Power BI, Power Pivot, and Analysis Services**](https://www.sqlbi.com/blog/marco/2016/01/26/create-static-tables-in-power-bi-power-pivot-and-analysis-services-powerbi-powerpivot-ssas-tabular/)

  A quick recap of all the methods available if you need a table with fixed static data in your data model. [» Read more](https://www.sqlbi.com/blog/marco/2016/01/26/create-static-tables-in-power-bi-power-pivot-and-analysis-services-powerbi-powerpivot-ssas-tabular/)
- [**Dynamic format strings with calculation groups**](https://www.sqlbi.com/articles/dynamic-format-strings-with-calculation-groups/)

  This article shows two techniques based on calculation groups: how to implement dynamic format strings in regular measures, and how to perform weight conversion on the fly. [» Read more](https://www.sqlbi.com/articles/dynamic-format-strings-with-calculation-groups/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/datatable-function](https://docs.microsoft.com/en-us/dax/datatable-function?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
