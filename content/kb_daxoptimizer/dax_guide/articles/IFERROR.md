---
title: "IFERROR"
function: "iferror"
category: "Logical"
url: "https://dax.guide/iferror/"
source: "dax.guide"
重要度:
难度:
---

# IFERROR DAX Function (Logical) Not recommended

Returns value\_if\_error if the first expression is an error and the value of the expression itself otherwise.

## Syntax

IFERROR ( <Value>, <ValueIfError> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Value |  | Any value or expression. |
| ValueIfError |  | Any value or expression. |

## Return values

Scalar A single value of any type.

A scalar of the same type as Value

## Remarks

The IFERROR function is a faster implementation of a semantically equivalent [[IF]] / [[ISERROR]] pattern.

```dax


IFERROR ( A, B )



-- Corresponds to



IF ( ISERROR ( A ), B, A )

```

IFERROR and [[ISERROR]] are not able to catch all runtime errors. Whether or not they can catch an error depends on the choice of execution plans. When the query plan chooses a faster execution (block mode), errors may not be captured. In light of their limitations, the two functions are discouraged.

Read [Appropriate use of error functions](https://learn.microsoft.com/en-us/dax/best-practices/dax-error-functions) for best practices about using IFERROR and [[ISERROR]].

[» 1 related article](#articles)  
[» 3 related functions](#alt)  

## Examples

```dax


--  IFERROR detects if the first argument produces an error.

--  In that case, it returns the second argument, without 

--  erroring out.

DEFINE

MEASURE Sales[Year Value unprotected] = 

    VAR CurrentYear = SELECTEDVALUE ( 'Date'[Calendar Year] )

    RETURN VALUE ( CurrentYear )

MEASURE Sales[Year Value] = 

    VAR CurrentYear = SELECTEDVALUE ( 'Date'[Calendar Year] )

    RETURN 

        IFERROR (

            INT ( VALUE ( CurrentYear ) ),

            INT ( VALUE ( RIGHT ( CurrentYear, 4 ) ) )

        )

EVALUATE

    SUMMARIZECOLUMNS ( 

        'Date'[Calendar Year], 

        // If you uncomment the following line, the query generates an error

        // "Year Value unprotected", [Year Value unprotected],

        "Year Value", [Year Value]

       )

ORDER BY [Calendar Year]

```

| Calendar Year | Year Value |
| --- | --- |
| 2005-01-01 | 2,005 |
| 2006-01-01 | 2,006 |
| 2007-01-01 | 2,007 |
| 2008-01-01 | 2,008 |
| 2009-01-01 | 2,009 |
| 2010-01-01 | 2,010 |
| 2011-01-01 | 2,011 |

## Related articles

Learn more about IFERROR in the following articles:

- [**Comparing different school terms in Power BI**](https://www.sqlbi.com/articles/comparing-different-school-terms-in-power-bi/)

  This article describes how to implement the comparison between school terms. The same technique can be applied to any arbitrary time periods that do not match regular months or quarters in a calendar, such as seasons or campaigns. [» Read more](https://www.sqlbi.com/articles/comparing-different-school-terms-in-power-bi/)

## Related functions

Other related functions are:

- [[IF]]
- [[ISERROR]]
- [[DIVIDE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/iferror-function-dax](https://docs.microsoft.com/en-us/dax/iferror-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
