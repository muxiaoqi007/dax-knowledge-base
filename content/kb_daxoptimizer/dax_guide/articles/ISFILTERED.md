---
title: "ISFILTERED"
function: "isfiltered"
category: "Information"
url: "https://dax.guide/isfiltered/"
source: "dax.guide"
重要度:
难度:
---

# ISFILTERED DAX Function (Information)

Returns true when there are direct filters on the specified column.

## Syntax

ISFILTERED ( <TableNameOrColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| TableNameOrColumnName |  | The table or column to check the filter info. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

TRUE when ColumnName is being filtered directly, or when any column of TableName is being filtered directly.

## Remarks

A column is said to be filtered directly when the filter or filters apply over the column.  
A column or table is said to be cross-filtered when a filter is applied to any column of the same table or in a related table.

ISFILTERED can check whether a column is being filtered directly or if any of the columns of the table is being filtered directly.

ISFILTERED supports a table argument since SSAS 2019 or Power BI April 2019. Former versions only support the column name argument.

[» 4 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  ISFILTERED checks whether a column has a direct filter

--  ISCROSSFILTERED checks whether a column has either a direct

--  or an indirect filter (which is a filter propagated from a related column)

--

--  In this example we place the filter on the 1 side of a 1:* relationship

EVALUATE

CALCULATETABLE (

    {

        ( "ISFILTERED Product[Color]",          ISFILTERED ( Product[Color] ) ),

        ( "ISFILTERED Sales[Quantity]",         ISFILTERED ( Sales[Quantity] ) ),

        ( "ISCROSSFILTERED Product[Category]",  ISCROSSFILTERED ( Product[Category] ) ),

        ( "ISCROSSFILTERED Product",            ISCROSSFILTERED ( Product ) ),

        ( "ISCROSSFILTERED Sales",              ISCROSSFILTERED ( Sales ) )

    },

    'Product'[Color] = "Red"

)

```

| Value1 | Value2 |
| --- | --- |
| ISFILTERED Product[Color] | true |
| ISFILTERED Sales[Quantity] | false |
| ISCROSSFILTERED Product[Category] | true |
| ISCROSSFILTERED Product | true |
| ISCROSSFILTERED Sales | true |

```dax


--  ISFILTERED checks whether a column has a direct filter

--  ISCROSSFILTERED checks whether a column has either a direct

--  or an indirect filter (which is a filter propagated from a related column)

--

--  In this example we place the filter on the many side of a 1:* relationship

EVALUATE

CALCULATETABLE (

    {

        ( "ISFILTERED Sales[Quantity]",         ISFILTERED ( Sales[Quantity] ) ),

        ( "ISFILTERED Product[Color]",          ISFILTERED ( Product[Color] ) ),

        ( "ISCROSSFILTERED Sales",              ISCROSSFILTERED ( Sales ) ),

        ( "ISCROSSFILTERED Product[Category]",  ISCROSSFILTERED ( Product[Category] ) ),

        ( "ISCROSSFILTERED Product",            ISCROSSFILTERED ( Product ) )

    },

    Sales[Quantity] = 1

)

```

| Value1 | Value2 |
| --- | --- |
| ISFILTERED Sales[Quantity] | true |
| ISFILTERED Product[Color] | false |
| ISCROSSFILTERED Sales | true |
| ISCROSSFILTERED Product[Category] | false |
| ISCROSSFILTERED Product | false |

```dax


--  ISFILTERED checks whether a column has a direct filter

--  ISCROSSFILTERED checks whether a column has either a direct

--  or an indirect filter (which is a filter propagated from a related column)

--

--  In this example we place the filter on the many side of a 1:* relationship

--  enabling bidirectional cross-filter

EVALUATE

CALCULATETABLE (

    {

        ( "ISFILTERED Sales[Quantity]",         ISFILTERED ( Sales[Quantity] ) ),

        ( "ISFILTERED Product[Color]",          ISFILTERED ( Product[Color] ) ),

        ( "ISCROSSFILTERED Sales",              ISCROSSFILTERED ( Sales ) ),

        ( "ISCROSSFILTERED Product[Category]",  ISCROSSFILTERED ( Product[Category] ) ),

        ( "ISCROSSFILTERED Product",            ISCROSSFILTERED ( Product ) )

    },

    Sales[Quantity] = 1,

    CROSSFILTER ( Sales[ProductKey], 'Product'[ProductKey], BOTH ) 

)

```

| Value1 | Value2 |
| --- | --- |
| ISFILTERED Sales[Quantity] | true |
| ISFILTERED Product[Color] | false |
| ISCROSSFILTERED Sales | true |
| ISCROSSFILTERED Product[Category] | true |
| ISCROSSFILTERED Product | true |

```dax


-- These queries return FALSE

EVALUATE { CALCULATE ( ISFILTERED ( Sales ), 'Product'[Color] = "Red" ) }

EVALUATE { CALCULATE ( ISFILTERED ( Sales[Quantity] ), 'Product'[Color] = "Red" ) }

EVALUATE { CALCULATE ( ISFILTERED ( Sales[Quantity] ), Sales[Unit Price] > 10 ) }



--These queries return TRUE

EVALUATE { CALCULATE ( ISFILTERED ( Sales ), Sales[Unit Price] > 10 ) }

EVALUATE { CALCULATE ( ISFILTERED ( Sales[Unit Price] ), Sales[Unit Price] > 10 ) }

```

## Related articles

Learn more about ISFILTERED in the following articles:

- [**Side effects of the Sort By Column setting in DAX**](https://www.sqlbi.com/articles/side-effects-in-dax-of-the-sort-by-column-setting/)

  The Sort By Column feature in Power BI causes side effects that are important to know when writing a DAX formula. This article explains these side effects and how to write correct DAX code to avoid getting incorrect results. [» Read more](https://www.sqlbi.com/articles/side-effects-in-dax-of-the-sort-by-column-setting/)
- [**Displaying filter context in Power BI Tooltips**](https://www.sqlbi.com/articles/displaying-filter-context-in-power-bi-tooltips/)

  This article describes how to display the filter context applied to a calculation using a special DAX measure in Power BI Tooltips. [» Read more](https://www.sqlbi.com/articles/displaying-filter-context-in-power-bi-tooltips/)
- [**Using cross-highlight with order and delivery dates in Power BI**](https://www.sqlbi.com/articles/using-cross-highlight-with-order-and-delivery-dates-in-power-bi/)

  This article describes how to enable the cross-highlight in Power BI charts using different dates for the same event, such as Order Date and Delivery Date. [» Read more](https://www.sqlbi.com/articles/using-cross-highlight-with-order-and-delivery-dates-in-power-bi/)
- [**Understanding parameter types in DAX user-defined functions (UDF)**](https://www.sqlbi.com/articles/understanding-parameter-types-in-dax-user-defined-functions-udf/)

  This article describes the parameter types available in DAX user-defined functions, focusing on the specialized reference types MEASUREREF, COLUMNREF, TABLEREF, and CALENDARREF. [» Read more](https://www.sqlbi.com/articles/understanding-parameter-types-in-dax-user-defined-functions-udf/)

## Related functions

Other related functions are:

- [[ISCROSSFILTERED]]
- [[ISINSCOPE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/isfiltered-function-dax](https://docs.microsoft.com/en-us/dax/isfiltered-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
