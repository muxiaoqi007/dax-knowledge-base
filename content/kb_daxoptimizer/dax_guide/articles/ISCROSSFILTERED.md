---
title: "ISCROSSFILTERED"
function: "iscrossfiltered"
category: "Information"
url: "https://dax.guide/iscrossfiltered/"
source: "dax.guide"
重要度:
难度:
---

# ISCROSSFILTERED DAX Function (Information)

Returns true when the specified table or column is crossfiltered.

## Syntax

ISCROSSFILTERED ( <TableNameOrColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| TableNameOrColumnName |  | The column or table to check the filter info. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

TRUE when any column of the table specified or of a related table is being filtered. Otherwise returns FALSE.

## Remarks

A column or table is said to be cross-filtered when a filter is applied to any column of the same table or in a related table.  
A column is said to be filtered directly when the filter or filters apply over the column.

[» 7 related articles](#articles)  
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

## Related articles

Learn more about ISCROSSFILTERED in the following articles:

- [**Clever Hierarchy Handling in DAX**](https://www.sqlbi.com/articles/clever-hierarchy-handling-in-dax/)

  Hierarchy handling in DAX is not very easy, due to the fact that hierarchies, unlike it was in MDX, are not first-class citizens in the DAX world. While hierarchies can be easily defined in the data model, there are no DAX functions that let you access, for example, the parent of the CurrentMember. Well, to tell the truth, there is no concept of CurrentMember in DAX either. [» Read more](https://www.sqlbi.com/articles/clever-hierarchy-handling-in-dax/)
- [**Implement Non Visual Totals with Power BI security roles**](https://www.sqlbi.com/articles/implement-non-visual-totals-with-power-bi-security-roles/)

  This article describes how to implement non-visual-totals with security roles in Power BI and Analysis Services Tabular, which by default show only visual totals of measures in the model. [» Read more](https://www.sqlbi.com/articles/implement-non-visual-totals-with-power-bi-security-roles/)
- [**Optimizing duplicated DAX expressions using variables**](https://www.sqlbi.com/articles/optimizing-duplicated-dax-expressions-using-variables/)

  This article describes how to use variables to optimize the performance of DAX expressions containing multiple instances of the same measure or the same sub-expression. [» Read more](https://www.sqlbi.com/articles/optimizing-duplicated-dax-expressions-using-variables/)
- [**Customizing default values for each user in Power BI reports**](https://www.sqlbi.com/articles/customizing-default-values-for-each-user-in-power-bi-reports/)

  This article shows how to use calculation groups to define a default set of values for columns in your model. Different users can have different default values, and yet retain the full capability to select different values. [» Read more](https://www.sqlbi.com/articles/customizing-default-values-for-each-user-in-power-bi-reports/)
- [**Optimizing incremental inventory calculations in DAX**](https://www.sqlbi.com/articles/optimizing-incremental-inventory-calculations-in-dax/)

  This article describes how to optimize inventory calculations in DAX by using snapshots to avoid the computational cost of a complete running total. [» Read more](https://www.sqlbi.com/articles/optimizing-incremental-inventory-calculations-in-dax/)
- [**Account receivable aging in Power BI**](https://www.sqlbi.com/articles/account-receivable-aging-in-power-bi/)

  This article describes an Accounts Receivable Aging report in Power BI, and shows how to simplify a business problem using existing modeling patterns. [» Read more](https://www.sqlbi.com/articles/account-receivable-aging-in-power-bi/)
- [**Controlling empty or multiple selections in calculation groups**](https://www.sqlbi.com/articles/controlling-empty-or-multiple-selections-in-calculation-groups/)

  This article describes how to control the execution of DAX code when there are either multiple or empty selections of calculation items in calculation groups. [» Read more](https://www.sqlbi.com/articles/controlling-empty-or-multiple-selections-in-calculation-groups/)

## Related functions

Other related functions are:

- [[ISFILTERED]]
- [[ISINSCOPE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/iscrossfiltered-function-dax](https://docs.microsoft.com/en-us/dax/iscrossfiltered-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
