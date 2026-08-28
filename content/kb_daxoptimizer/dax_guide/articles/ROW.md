---
title: "ROW"
function: "row"
category: "Table manipulation"
url: "https://dax.guide/row/"
source: "dax.guide"
重要度:
难度:
---

# ROW DAX Function (Table manipulation)

Returns a single row table with new columns specified by the DAX expressions.

## Syntax

ROW ( <Name>, <Expression> [, <Name>, <Expression> [, … ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Name | Repeatable | Name of the new column. |
| Expression | Repeatable | The expression for the column. |

## Return values

Table An entire table or a table with one or more columns.

A single row table.

## Remarks

ROW does not preserve the [data lineage](https://www.sqlbi.com/articles/understanding-data-lineage-in-dax/) of the returned columns returned, even though a column expression is a simple column reference.

[» 2 related articles](#articles)  

## Examples

```dax


--  ROW generates a table with one row only.

--  You provide the name of the columns and their values.

EVALUATE

    ROW ( 

        "Sales in 2009", CALCULATE ( [Sales Amount], 'Date'[Calendar Year] = "CY 2009" ),

        "Sales in 2008", CALCULATE ( [Sales Amount], 'Date'[Calendar Year] = "CY 2008" )

    )

```

| Sales in 2009 | Sales in 2008 |
| --- | --- |
| 9,353,814.87 | 9,927,582.99 |

```dax


--  Expressions evaluated in ROW respect the filter context.

EVALUATE

CALCULATETABLE (

    ROW (

        "Sales in 2009", CALCULATE ( [Sales Amount], 'Date'[Calendar Year] = "CY 2009" ),

        "Sales in 2008", CALCULATE ( [Sales Amount], 'Date'[Calendar Year] = "CY 2008" )

    ),

    'Product'[Brand] = "Contoso"

)

```

| Sales in 2009 | Sales in 2008 |
| --- | --- |
| 2,253,412.80 | 2,369,167.68 |

```dax


--  ROW controls the name of the columns, whereas the table constructor

--  automatically assigns column names. You must use SELECTCOLUMNS to control

--  the column names of a table constructor as you do in ROW.

DEFINE

    VAR Sales_2009_2008 =

        {

             ( CALCULATE ( [Sales Amount], 'Date'[Calendar Year] = "CY 2009" ), CALCULATE ( [Sales Amount], 'Date'[Calendar Year] = "CY 2008" ) )

        }

    VAR RenamedColumns =

        SELECTCOLUMNS (

            Sales_2009_2008,

            "Sales in 2009", [Value1],

            "Sales in 2008", [Value2]

        )



EVALUATE

Sales_2009_2008



EVALUATE

RenamedColumns

```

| Value1 | Value2 |
| --- | --- |
| 9,353,814.87 | 9,927,582.99 |

| Sales in 2009 | Sales in 2008 |
| --- | --- |
| 9,353,814.87 | 9,927,582.99 |

## Related articles

Learn more about ROW in the following articles:

- [**Using GENERATE and ROW instead of ADDCOLUMNS in DAX**](https://www.sqlbi.com/articles/using-generate-and-row-instead-of-addcolumns-in-dax/)

  This article explains how to improve DAX queries using GENERATE and ROW instead of ADDCOLUMNS when you create table expressions. [» Read more](https://www.sqlbi.com/articles/using-generate-and-row-instead-of-addcolumns-in-dax/)
- [**Computing accurate percentages with row-level security in Power BI**](https://www.sqlbi.com/articles/computing-accurate-percentages-with-row-level-security-in-power-bi/)

  This article shows how to compute ratios when row-level security hides some of the data. If the percentage also includes the hidden rows in the comparison, you should customize the data model and the measures involved to get the right result. [» Read more](https://www.sqlbi.com/articles/computing-accurate-percentages-with-row-level-security-in-power-bi/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/row-function-dax](https://docs.microsoft.com/en-us/dax/row-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
