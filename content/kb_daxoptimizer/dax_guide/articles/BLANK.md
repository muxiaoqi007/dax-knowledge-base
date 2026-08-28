---
title: "BLANK"
function: "blank"
url: "https://dax.guide/blank/"
source: "dax.guide"
重要度:
难度:
---

# BLANK DAX Function

Returns a blank.

## Syntax

BLANK ( )

This expression has no parameters.

## Return values

Scalar A single value of any type.

The BLANK value does not have a data type.

## Remarks

The BLANK value is automatically converted in case it is compared with other values.  
The right way to check whether a value is BLANK is by using either the [operator ==](https://dax.guide/op/strictly-equal-to/) or the [[ISBLANK]] function. Do not use the operator “=”.  
The [operator ==](https://dax.guide/op/strictly-equal-to/) is a “strictly equal to” operator that considers BLANK as a different value other than 0 or an empty string.  
See related articles for more details.

[» 6 related articles](#articles)  

## Examples

```dax


--  BLANK returns the BLANK value

--

--  It is mostly useful to blank out the result of a calculation

--  or to perform comparisons.

DEFINE

    MEASURE Customer[EmptyNames] =

        CALCULATE (

            COUNTROWS ( Customer ),

            Customer[Customer Name] == BLANK ()

        )

EVALUATE

SUMMARIZECOLUMNS (

    Customer[Continent],

    "Customers", COUNTROWS ( Customer ),

    "Customers with blank name", [EmptyNames]

)

```

| Continent | Customers | Customers with blank name |
| --- | --- | --- |
| Asia | 3,658 | 34 |
| North America | 9,665 | 138 |
| Europe | 5,546 | 21 |

```dax


--  BLANK is useful also to provide BLANK arguments to some functions

--  like DATESBETWEEN.

EVALUATE

    DATESBETWEEN ( 

        'Date'[Date],

        DATE ( 2011, 12, 20 ),

        BLANK ()

    )

ORDER BY 'Date'[Date]

```

| Date |
| --- |
| 2011-12-20 |
| 2011-12-21 |
| 2011-12-22 |
| 2011-12-23 |
| 2011-12-24 |
| 2011-12-25 |
| 2011-12-26 |
| 2011-12-27 |
| 2011-12-28 |
| 2011-12-29 |
| 2011-12-30 |
| 2011-12-31 |

```dax


--  BLANK is equal to 0 and to an empty string in DAX.

--  You need to use == to check for "strictly equal to"

EVALUATE

    { 

        ( "Blank = 0", BLANK () = 0 ),

        ( "Blank = """"", BLANK () = "" ),

        ( "Blank == 0", BLANK () == 0 ),

        ( "Blank == """"", BLANK () == "" ) 

    }

```

| Value1 | Value2 |
| --- | --- |
| Blank = 0 | true |
| Blank = “” | true |
| Blank == 0 | false |
| Blank == “” | false |

## Related articles

Learn more about BLANK in the following articles:

- [**How to handle BLANK in DAX measures**](https://www.sqlbi.com/articles/how-to-handle-blank-in-dax-measures/)

  This article describes a counterintuitive behavior of BLANK in DAX measures affecting Power BI, Analysis Services, and Power Pivot. That behavior could cause mistakes in a report using alternate expressions of the same calculation. Indeed, these expressions are not equivalent when BLANK is involved. [» Read more](https://www.sqlbi.com/articles/how-to-handle-blank-in-dax-measures/)
- [**Handling BLANK in DAX**](https://www.sqlbi.com/articles/blank-handling-in-dax/)

  The blank value in DAX is a special value requiring particular attention in comparisons. It is not like the special null value in SQL, and it could appear in any conversion from a table expression. This article explores in details the behavior of the blank value in DAX, highlighting a common error in DAX expressions […] [» Read more](https://www.sqlbi.com/articles/blank-handling-in-dax/)
- [**Blank row in DAX**](https://www.sqlbi.com/articles/blank-row-in-dax/)

  There are two functions in DAX that return the list of values of a column: VALUES and DISTINCT. This article describes the difference between the two, explaining the details of the blank row added to tables for invalid relationships. [» Read more](https://www.sqlbi.com/articles/blank-row-in-dax/)
- [**Optimizing conditions involving blank values in DAX**](https://www.sqlbi.com/articles/optimizing-conditions-involving-blank-values-in-dax/)

  This article describes how blank values considered in a DAX conditional expression can affect its query plan and how to apply possible optimizations to improve performance in these cases. [» Read more](https://www.sqlbi.com/articles/optimizing-conditions-involving-blank-values-in-dax/)
- [**From SQL to DAX: Implementing NULLIF and COALESCE in DAX**](https://www.sqlbi.com/articles/from-sql-to-dax-implementing-nullif-and-coalesce-in-dax/)

  This article describes how to implement a syntax equivalent to the T-SQL function NULLIF and the ANSI SQL function COALESCE, in DAX. [» Read more](https://www.sqlbi.com/articles/from-sql-to-dax-implementing-nullif-and-coalesce-in-dax/)
- [**How to return BLANK instead of zero**](https://www.sqlbi.com/articles/how-to-return-blank-instead-of-zero/)

  This article describes how to return BLANK instead of zero in a DAX measure. Using this technique, you can remove rows in a Power BI matrix visual where the result of a measure is zero. [» Read more](https://www.sqlbi.com/articles/how-to-return-blank-instead-of-zero/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/blank-function-dax](https://docs.microsoft.com/en-us/dax/blank-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
