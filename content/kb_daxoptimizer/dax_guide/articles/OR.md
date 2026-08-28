---
title: "OR"
function: "or"
category: "Logical"
url: "https://dax.guide/or/"
source: "dax.guide"
重要度:
难度:
---

# OR DAX Function (Logical)

Returns TRUE if any of the arguments are TRUE, and returns FALSE if all arguments are FALSE.

## Syntax

OR ( <Logical1>, <Logical2> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Logical1 |  | The logical values you want to test. |
| Logical2 |  | The logical values you want to test. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

The value is TRUE if any of the two arguments is TRUE; the value is FALSE if both the arguments are FALSE.

## Remarks

The OR function accepts only two arguments. Consider using the [operator ||](https://dax.guide/op/or/) to avoid multiple nested calls in case there are three or more conditions to evaluate in a logical OR.

[» 2 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  TRUE and FALSE are the two constant values of True and False (1, 0 )

--  AND performs the logical AND between two conditions

--  OR performs the logical OR between two conditions

--  NOT performs logical negation

EVALUATE

    {

        ( "FALSE", FALSE ),

        ( "FALSE()", FALSE() ),

        ( "TRUE", TRUE ),

        ( "TRUE()", TRUE() ),

        ( "AND ( TRUE, FALSE )", AND ( TRUE, FALSE ) ),

        ( "OR ( TRUE, FALSE )", OR ( TRUE, FALSE ) ),

        ( "NOT ( TRUE )", NOT ( TRUE ) ),

        ( "NOT ( FALSE )", NOT ( FALSE ) )        

    }

```

| Value1 | Value2 |
| --- | --- |
| FALSE | false |
| FALSE() | false |
| TRUE | true |
| TRUE() | true |
| AND ( TRUE, FALSE ) | false |
| OR ( TRUE, FALSE ) | true |
| NOT ( TRUE ) | false |
| NOT ( FALSE ) | true |

```dax


--  The AND function can be replaced with the && operator

--  The OR function can be replaced with the || operator

EVALUATE

    {

        ( "AND ( TRUE, FALSE )", AND ( TRUE, FALSE ) ),

        ( "TRUE && FALSE", TRUE && FALSE ),

        ( "OR ( TRUE, FALSE )", OR ( TRUE, FALSE ) ),

        ( "TRUE || FALSE", TRUE || FALSE )

    }

```

| Value1 | Value2 |
| --- | --- |
| AND ( TRUE, FALSE ) | false |
| TRUE && FALSE | false |
| OR ( TRUE, FALSE ) | true |
| TRUE || FALSE | true |

```dax


--  Operators are more convenient when you need to combine

--  more than two conditions.

EVALUATE

    VAR A = TRUE

    VAR B = FALSE

    VAR C = TRUE

RETURN

    {

        ( "Using OR", OR ( A, OR ( B, C ) ) ),

        ( "Using ||",  A|| B || C )

    }

```

| Value1 | Value2 |
| --- | --- |
| Using OR | true |
| Using || | true |

## Related articles

Learn more about OR in the following articles:

- [**Using OR conditions between slicers in DAX**](https://www.sqlbi.com/articles/using-or-conditions-between-slicers-in-dax/)

  This article describes how to implement in DAX a logical OR condition between the selection of two slicers of a Power BI report or of a PivotTable in Excel. By default, when relying on more than one slicer they are considered in an AND condition. [» Read more](https://www.sqlbi.com/articles/using-or-conditions-between-slicers-in-dax/)
- [**Using tuple syntax in DAX expressions**](https://www.sqlbi.com/articles/using-tuple-syntax-in-dax-expressions/)

  This article describes the use of the tuple syntax in DAX expressions to simplify comparisons involving two or more columns. [» Read more](https://www.sqlbi.com/articles/using-tuple-syntax-in-dax-expressions/)

## Related functions

Other related functions are:

- [[AND]]
- [[NOT]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/or-function-dax](https://docs.microsoft.com/en-us/dax/or-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
