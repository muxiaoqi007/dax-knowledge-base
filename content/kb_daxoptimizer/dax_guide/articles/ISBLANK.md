---
title: "ISBLANK"
function: "isblank"
category: "Information"
url: "https://dax.guide/isblank/"
source: "dax.guide"
重要度:
难度:
---

# ISBLANK DAX Function (Information)

Checks whether a value is blank, and returns TRUE or FALSE.

## Syntax

ISBLANK ( <Value> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Value |  | The value you want to test. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

A Boolean value of TRUE if the value is blank; otherwise FALSE.

## Remarks

The comparison with blank is also possible with the “strictly equal to” [operator ==](https://dax.guide/op/strictly-equal-to/) as shown in the two following correspondent predicates.

```dax


ISBLANK ( <expression> )  -- same result as the following line

<expression> == BLANK()   -- same result as the previous line

```

[» 9 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  ISBLANK check that an expression is strictly equal to BLANK

--  ISBLANK (x) is an alias for x == BLANK ()

--  An empty string and 0 are not considered blank by ISBLANK

DEFINE

    VAR BlankValue = BLANK()

    VAR NumericValue = 42

    VAR ZeroValue = 0

    VAR EmptyString = ""

EVALUATE {

 ( 1, "ISBLANK ( BlankValue )", ISBLANK ( BlankValue ) ),

 ( 2, "ISBLANK ( NumericValue )", ISBLANK ( NumericValue ) ),

 ( 3, "ISBLANK ( ZeroValue )", ISBLANK ( ZeroValue ) ),

 ( 4, "ISBLANK ( EmptyString )", ISBLANK ( EmptyString ) ),

 ( 5, "EmptyString == BLANK()", EmptyString == BLANK() ),

 ( 6, "EmptyString = BLANK()", EmptyString = BLANK() )

}

ORDER BY [Value1]

```

| Value1 | Value2 | Value3 |
| --- | --- | --- |
| 1 | ISBLANK ( BlankValue ) | true |
| 2 | ISBLANK ( NumericValue ) | false |
| 3 | ISBLANK ( ZeroValue ) | false |
| 4 | ISBLANK ( EmptyString ) | false |
| 5 | EmptyString == BLANK() | false |
| 6 | EmptyString = BLANK() | true |

```dax


--  ISBLANK cannot be used with tables, it requires a scalar value

--  using it with tables forces the implicit conversion of a table

--  to a scalar value and might result in an error

DEFINE

    VAR EmptySet = FILTER ( { 1 }, FALSE )

    VAR SetWithBlank = { BLANK() }

EVALUATE {

    ( 1, "ISBLANK ( EmptySet )", ISBLANK ( EmptySet ) ),

    ( 2, "ISBLANK ( SetWithBlank )", ISBLANK ( SetWithBlank ) )

}

ORDER BY [Value1]

```

| Value1 | Value2 | Value3 |
| --- | --- | --- |
| 1 | ISBLANK ( EmptySet ) | true |
| 2 | ISBLANK ( SetWithBlank ) | true |

```dax


--  ISBLANK check that an expression is strictly equal to BLANK

--  ISBLANK (x) is an alias for x == BLANK ()

DEFINE

    MEASURE Customer[EmptyNames] =      -- Returns blanks and empty string

        CALCULATE (

            COUNTROWS ( Customer ),

            Customer[Customer Name] = BLANK ()

        )

    MEASURE Customer[BlankNames] =      -- Returns only blanks, same as == BLANK()

        CALCULATE (

            COUNTROWS ( Customer ),

            ISBLANK ( Customer[Customer Name] )

        )

EVALUATE

SUMMARIZECOLUMNS (

    Customer[Continent],

    "Customers", COUNTROWS ( Customer ),

    "Customers with empty name", [EmptyNames],

    "Customers with blank name", [BlankNames]

)

```

| Continent | Customers | Customers with empty name | Customers with blank name |
| --- | --- | --- | --- |
| Asia | 3,658 | 67 | 34 |
| North America | 9,665 | 276 | 138 |
| Europe | 5,546 | 42 | 21 |

## Related articles

Learn more about ISBLANK in the following articles:

- [**How to handle BLANK in DAX measures**](https://www.sqlbi.com/articles/how-to-handle-blank-in-dax-measures/)

  This article describes a counterintuitive behavior of BLANK in DAX measures affecting Power BI, Analysis Services, and Power Pivot. That behavior could cause mistakes in a report using alternate expressions of the same calculation. Indeed, these expressions are not equivalent when BLANK is involved. [» Read more](https://www.sqlbi.com/articles/how-to-handle-blank-in-dax-measures/)
- [**Handling BLANK in DAX**](https://www.sqlbi.com/articles/blank-handling-in-dax/)

  The blank value in DAX is a special value requiring particular attention in comparisons. It is not like the special null value in SQL, and it could appear in any conversion from a table expression. This article explores in details the behavior of the blank value in DAX, highlighting a common error in DAX expressions […] [» Read more](https://www.sqlbi.com/articles/blank-handling-in-dax/)
- [**Check Empty Table Condition with DAX**](https://www.sqlbi.com/articles/check-empty-table-condition-with-dax/)

  In DAX there are different ways to test whether a table is empty. This test can be used in complex DAX expressions and this short article briefly discuss what are the suggested approaches from a performance perspective. [» Read more](https://www.sqlbi.com/articles/check-empty-table-condition-with-dax/)
- [**Hiding future dates for calculations in DAX**](https://www.sqlbi.com/articles/hiding-future-dates-for-calculations-in-dax/)

  This article describes how to write DAX measures that compute aggregations or comparisons with past dates without showing or comparing future dates. [» Read more](https://www.sqlbi.com/articles/hiding-future-dates-for-calculations-in-dax/)
- [**Optimizing conditions involving blank values in DAX**](https://www.sqlbi.com/articles/optimizing-conditions-involving-blank-values-in-dax/)

  This article describes how blank values considered in a DAX conditional expression can affect its query plan and how to apply possible optimizations to improve performance in these cases. [» Read more](https://www.sqlbi.com/articles/optimizing-conditions-involving-blank-values-in-dax/)
- [**From SQL to DAX: Implementing NULLIF and COALESCE in DAX**](https://www.sqlbi.com/articles/from-sql-to-dax-implementing-nullif-and-coalesce-in-dax/)

  This article describes how to implement a syntax equivalent to the T-SQL function NULLIF and the ANSI SQL function COALESCE, in DAX. [» Read more](https://www.sqlbi.com/articles/from-sql-to-dax-implementing-nullif-and-coalesce-in-dax/)
- [**The COALESCE function in DAX**](https://www.sqlbi.com/articles/the-coalesce-function-in-dax/)

  COALESCE is a DAX function introduced in March 2020. This article describes the purpose of COALESCE and how to simplify DAX expressions by removing verbose conditions, and yet obtain the same result. [» Read more](https://www.sqlbi.com/articles/the-coalesce-function-in-dax/)
- [**Preparing a data model for Sankey Charts in Power BI**](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)

  This article describes how to correctly shape a data model and prepare data to use a Sankey Chart as a funnel, considering events related to a customer (contact, trial, subscription, renewal, and others). [» Read more](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)
- [**Blank in date columns and DAX time intelligence functions**](https://www.sqlbi.com/articles/blank-in-date-columns-and-dax-time-intelligence-functions/)

  This article explores the implications of having blank values in date columns and provides the best practices for managing them in DAX calculations and Power BI reports. [» Read more](https://www.sqlbi.com/articles/blank-in-date-columns-and-dax-time-intelligence-functions/)

## Related functions

Other related functions are:

- [[BLANK]]
- [[ISEMPTY]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/isblank-function-dax](https://docs.microsoft.com/en-us/dax/isblank-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
