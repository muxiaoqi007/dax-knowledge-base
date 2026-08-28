---
title: "IF"
function: "if"
category: "Logical"
url: "https://dax.guide/if/"
source: "dax.guide"
重要度:
难度:
---

# IF DAX Function (Logical)

Checks whether a condition is met, and returns one value if TRUE, and another value if FALSE.

## Syntax

IF ( <LogicalTest>, <ResultIfTrue> [, <ResultIfFalse>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| LogicalTest |  | Any value or expression that can be evaluated to TRUE or FALSE. |
| ResultIfTrue |  | The value that is returned if the logical test is TRUE. |
| ResultIfFalse | Optional | The value that is returned if the logical test is FALSE.  If omitted, a [[BLANK]] is returned. |

## Return values

Scalar A single value of any type.

Either ResultIfTrue or ResultIfFalse expression result, depending on LogicalTest.

## Remarks

Using IF can generate multiple branches of code execution that could result in slower performance at query time.  
Then IF can return [[BLANK]] as one of the results, there are cases where using [[DIVIDE]] to obtain the same result could produce a faster query plan.  
The following code returns [[BLANK]] if LogicalTest is false.

```dax


IF ( <LogicalTest>, <ResultIfTrue> )

```

The previous code is semantically equivalent to the following expression:

```dax


DIVIDE ( <ResultIfTrue>, <LogicalTest> )

```

[» 7 related articles](#articles)  
[» 3 related functions](#alt)  

## Examples

```dax


--  IF evaluates one of two branches, depending on the condition

EVALUATE

ADDCOLUMNS (

    VALUES ( 'Product'[Brand] ),

    "Sales 1", [Sales Amount],

    "Sales 2",

        VAR SalesAmount = [Sales Amount]

        RETURN

            IF ( SalesAmount > 3000000, 3000000, SalesAmount ),

    "Sales 3", MIN ( [Sales Amount], 3000000 )

)

ORDER BY 'Product'[Brand]

```

| Brand | Sales 1 | Sales 2 | Sales 3 |
| --- | --- | --- | --- |
| A. Datum | 2,096,184.64 | 2,096,184.64 | 2,096,184.64 |
| Adventure Works | 4,011,112.28 | 3,000,000.00 | 3,000,000.00 |
| Contoso | 7,352,399.03 | 3,000,000.00 | 3,000,000.00 |
| Fabrikam | 5,554,015.73 | 3,000,000.00 | 3,000,000.00 |
| Litware | 3,255,704.03 | 3,000,000.00 | 3,000,000.00 |
| Northwind Traders | 1,040,552.13 | 1,040,552.13 | 1,040,552.13 |
| Proseware | 2,546,144.16 | 2,546,144.16 | 2,546,144.16 |
| Southridge Video | 1,384,413.85 | 1,384,413.85 | 1,384,413.85 |
| Tailspin Toys | 325,042.42 | 325,042.42 | 325,042.42 |
| The Phone Company | 1,123,819.07 | 1,123,819.07 | 1,123,819.07 |
| Wide World Importers | 1,901,956.66 | 1,901,956.66 | 1,901,956.66 |

```dax


--  The "else" branch is optional. 

--  If not specified, it defaults to BLANK

EVALUATE

ADDCOLUMNS (

    VALUES ( 'Product'[Brand] ),

    "Sales 1", [Sales Amount],

    "Sales 2",

        VAR SalesAmount = [Sales Amount]

        RETURN

            IF ( SalesAmount > 3000000, 3000000 )

)

ORDER BY 'Product'[Brand]

```

| Brand | Sales 1 | Sales 2 |
| --- | --- | --- |
| A. Datum | 2,096,184.64 | (Blank) |
| Adventure Works | 4,011,112.28 | 3,000,000 |
| Contoso | 7,352,399.03 | 3,000,000 |
| Fabrikam | 5,554,015.73 | 3,000,000 |
| Litware | 3,255,704.03 | 3,000,000 |
| Northwind Traders | 1,040,552.13 | (Blank) |
| Proseware | 2,546,144.16 | (Blank) |
| Southridge Video | 1,384,413.85 | (Blank) |
| Tailspin Toys | 325,042.42 | (Blank) |
| The Phone Company | 1,123,819.07 | (Blank) |
| Wide World Importers | 1,901,956.66 | (Blank) |

```dax


--  The two branches of IF can use different datatypes for

--  their result. In that case, the IF function returns a

--  VARIANT datatype

EVALUATE

ADDCOLUMNS (

    VALUES ( 'Product'[Brand] ),

    "Sales 1", [Sales Amount],

    "Sales 2",

        VAR SalesAmount = [Sales Amount]

        RETURN

            IF ( SalesAmount > 3000000, 3000000, "Less than 3K" )

)

ORDER BY 'Product'[Brand]

```

| Brand | Sales 1 | Sales 2 |
| --- | --- | --- |
| A. Datum | 2,096,184.64 | Less than 3K |
| Adventure Works | 4,011,112.28 | 3000000 |
| Contoso | 7,352,399.03 | 3000000 |
| Fabrikam | 5,554,015.73 | 3000000 |
| Litware | 3,255,704.03 | 3000000 |
| Northwind Traders | 1,040,552.13 | Less than 3K |
| Proseware | 2,546,144.16 | Less than 3K |
| Southridge Video | 1,384,413.85 | Less than 3K |
| Tailspin Toys | 325,042.42 | Less than 3K |
| The Phone Company | 1,123,819.07 | Less than 3K |
| Wide World Importers | 1,901,956.66 | Less than 3K |

IF returns a scalar value, it cannot be used to return a table.  
DAX tries to convert the table to a scalar value, and it fails if there are multiple rows. The following code query an error.

```dax


EVALUATE

VAR SelectedYear = SELECTEDVALUE ( 'Date'[Calendar Year Number] )

VAR CurrentYear = YEAR ( TODAY () )

VAR Period = 

    IF ( 

        SelectedYear = CurrentYear,

        DATESYTD ( 'Date'[Date] ),      -- Table

        VALUES ( 'Date'[Date] )         -- Table

    )

VAR Result = CALCULATE ( [Sales Amount], Period ) 

RETURN { Result }

```

## Related articles

Learn more about IF in the following articles:

- [**Optimizing IF conditions by using variables**](https://www.sqlbi.com/articles/optimizing-if-conditions-using-variables/)

  This article describes a very common optimization pattern that relies on variables to optimize conditional expressions in DAX. [» Read more](https://www.sqlbi.com/articles/optimizing-if-conditions-using-variables/)
- [**Understanding eager vs. strict evaluation in DAX**](https://www.sqlbi.com/articles/understanding-eager-vs-strict-evaluation-in-dax/)

  This article describes the differences between eager evaluation and strict evaluation in DAX, empowering you to choose the best evaluation type for your data models. [» Read more](https://www.sqlbi.com/articles/understanding-eager-vs-strict-evaluation-in-dax/)
- [**Optimizing IF and SWITCH expressions using variables**](https://www.sqlbi.com/articles/optimizing-if-and-switch-expressions-using-variables/)

  This article describes how variables should be used in DAX expressions involving IF and SWITCH statements in order to improve performance. [» Read more](https://www.sqlbi.com/articles/optimizing-if-and-switch-expressions-using-variables/)
- [**From SQL to DAX: Implementing NULLIF and COALESCE in DAX**](https://www.sqlbi.com/articles/from-sql-to-dax-implementing-nullif-and-coalesce-in-dax/)

  This article describes how to implement a syntax equivalent to the T-SQL function NULLIF and the ANSI SQL function COALESCE, in DAX. [» Read more](https://www.sqlbi.com/articles/from-sql-to-dax-implementing-nullif-and-coalesce-in-dax/)
- [**Optimizing mutually exclusive calculations**](https://www.sqlbi.com/articles/optimizing-mutually-exclusive-calculations/)

  This article describes how to optimize DAX expressions with mutually exclusive calculations that might cause slow query performance. [» Read more](https://www.sqlbi.com/articles/optimizing-mutually-exclusive-calculations/)
- [**Optimizing conditions involving blank values in DAX**](https://www.sqlbi.com/articles/optimizing-conditions-involving-blank-values-in-dax/)

  This article describes how blank values considered in a DAX conditional expression can affect its query plan and how to apply possible optimizations to improve performance in these cases. [» Read more](https://www.sqlbi.com/articles/optimizing-conditions-involving-blank-values-in-dax/)
- [**Understanding how DAX evaluates IF statements**](https://www.sqlbi.com/articles/understanding-how-dax-evaluates-if-statements/)

  In this article, we examine the details of executing DAX code, with a focus on how the IF function is implemented in various scenarios. [» Read more](https://www.sqlbi.com/articles/understanding-how-dax-evaluates-if-statements/)

## Related functions

Other related functions are:

- [[COALESCE]]
- [[IF.EAGER]]
- [[SWITCH]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/if-function-dax](https://docs.microsoft.com/en-us/dax/if-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
