---
title: "COALESCE"
function: "coalesce"
category: "Logical"
url: "https://dax.guide/coalesce/"
source: "dax.guide"
重要度:
难度:
---

# COALESCE DAX Function (Logical)

Returns the first argument that does not evaluate to a blank value. If all arguments evaluate to blank values, [[BLANK]] is returned.

## Syntax

COALESCE ( <Value1>, <Value2> [, <ValueN> [, … ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Value1 |  | Any value or expression. |
| Value2 | Repeatable | Any value or expression. |

## Return values

Scalar A single value of any type.

The first Value argument that is not blank.

[» 4 related articles](#articles)  
[» 3 related functions](#alt)  

## Examples

```dax


--  COALESCE returns the first non-blank of its arguments

--  It is commonly used to provide default values to expressions

--  that might result in a blank

EVALUATE

SELECTCOLUMNS (

    TOPN ( 10, Store ),

    "Store name", Store[Store Name],

    "Manager",

        COALESCE ( Store[Area Manager], "** Not Assigned **" ),

    "Years open",

        DATEDIFF (

            Store[Open Date],

            COALESCE ( Store[Close Date], TODAY () ),

            YEAR

        )

)

ORDER BY [Manager]

```

| Store name | Manager | Years open |
| --- | --- | --- |
| Contoso Aurora Store | \*\* Not Assigned \*\* | 18 |
| Contoso Bar Harbor Store | Alvarez, Janet | 17 |
| Contoso Renton Store | Bennett, Sydney | 17 |
| Contoso Spokane Store | Hill, Wyatt | 17 |
| Contoso Bayonne Store | Hill, Wyatt | 17 |
| Contoso Seattle No.2 Store | Huang, Eugene | 17 |
| Contoso Redmond Store | Johnson, Elizabeth | 17 |
| Contoso Attleboro Store | Moreno, Jimmy | 17 |
| Contoso Baltimore Store | Ramos, Theresa | 17 |
| Contoso Back River Store | Russell, Jennifer | 17 |

```dax


--  COALESCE can have more than two arguments

EVALUATE

VAR A = BLANK ()

VAR B = BLANK ()

VAR C = "Test"

RETURN

    { COALESCE ( A, B, C ) }



```

| Value |
| --- |
| Test |

## Related articles

Learn more about COALESCE in the following articles:

- [**From SQL to DAX: Implementing NULLIF and COALESCE in DAX**](https://www.sqlbi.com/articles/from-sql-to-dax-implementing-nullif-and-coalesce-in-dax/)

  This article describes how to implement a syntax equivalent to the T-SQL function NULLIF and the ANSI SQL function COALESCE, in DAX. [» Read more](https://www.sqlbi.com/articles/from-sql-to-dax-implementing-nullif-and-coalesce-in-dax/)
- [**The COALESCE function in DAX**](https://www.sqlbi.com/articles/the-coalesce-function-in-dax/)

  COALESCE is a DAX function introduced in March 2020. This article describes the purpose of COALESCE and how to simplify DAX expressions by removing verbose conditions, and yet obtain the same result. [» Read more](https://www.sqlbi.com/articles/the-coalesce-function-in-dax/)
- [**Preparing a data model for Sankey Charts in Power BI**](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)

  This article describes how to correctly shape a data model and prepare data to use a Sankey Chart as a funnel, considering events related to a customer (contact, trial, subscription, renewal, and others). [» Read more](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)
- [**Using EXPAND and COLLAPSE in visual calculations**](https://www.sqlbi.com/articles/using-expand-and-collapse-in-visual-calculations/)

  This article provides examples of visual calculations where the use of EXPAND and COLLAPSE is required to obtain the correct result. [» Read more](https://www.sqlbi.com/articles/using-expand-and-collapse-in-visual-calculations/)

## Related functions

Other related functions are:

- [[IF]]
- [[IF.EAGER]]
- [[SWITCH]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/coalesce-function-dax](https://docs.microsoft.com/en-us/dax/coalesce-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
