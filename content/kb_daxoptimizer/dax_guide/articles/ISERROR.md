---
title: "ISERROR"
function: "iserror"
category: "Information"
url: "https://dax.guide/iserror/"
source: "dax.guide"
重要度:
难度:
---

# ISERROR DAX Function (Information) Not recommended

Checks whether a value is an error, and returns TRUE or FALSE.

## Syntax

ISERROR ( <Value> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Value |  | The value you want to test. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

A Boolean value of TRUE if the value is an error; otherwise FALSE.

## Remarks

[[IFERROR]] and ISERROR are not able to catch all runtime errors. Whether or not they can catch an error depends on the choice of execution plans. When the query plan chooses a faster execution (block mode), errors may not be captured. In light of their limitations, the two functions are discouraged.

Read [Appropriate use of error functions](https://learn.microsoft.com/en-us/dax/best-practices/dax-error-functions) for best practices about using [[IFERROR]] and ISERROR.

[» 1 related function](#alt)  

## Examples

```dax


--  ISERROR detects if its argument produces an error.

--

--  It is commonly replaced by IFERROR, that includes in the 

--  same function both IF and ISERROR.

DEFINE

MEASURE Sales[Year Value unprotected] = 

    VAR CurrentYear = SELECTEDVALUE ( 'Date'[Calendar Year] )

    RETURN VALUE ( CurrentYear )

MEASURE Sales[Year Value] = 

    VAR CurrentYear = SELECTEDVALUE ( 'Date'[Calendar Year] )

    RETURN 

        IF (

            ISERROR ( INT ( VALUE ( CurrentYear ) ) ),

            INT ( VALUE ( RIGHT ( CurrentYear, 4 ) ) ),

            INT ( VALUE ( CurrentYear ) )

        )

EVALUATE

    SUMMARIZECOLUMNS ( 

        'Date'[Calendar Year], 

        --"Year Value unprotected", [Year Value unprotected]

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

## Related functions

Other related functions are:

- [[IFERROR]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Imke Feldmann

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/iserror-function-dax](https://docs.microsoft.com/en-us/dax/iserror-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
