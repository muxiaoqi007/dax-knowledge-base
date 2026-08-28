---
title: "AND"
function: "and"
category: "Logical"
url: "https://dax.guide/and/"
source: "dax.guide"
重要度:
难度:
---

# AND DAX Function (Logical)

Checks whether all arguments are TRUE, and returns TRUE if all arguments are TRUE.

## Syntax

AND ( <Logical1>, <Logical2> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Logical1 |  | The logical values you want to test. |
| Logical2 |  | The logical values you want to test. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

## Remarks

The AND function accepts only two arguments. Consider using the [operator &&](https://dax.guide/op/and/) to avoid multiple nested calls in case there are three or more conditions to evaluate in a logical AND.

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

        ( "Using AND", AND ( A, AND ( B, C ) ) ),

        ( "Using &&",  A && B && C )

    }

```

| Value1 | Value2 |
| --- | --- |
| Using AND | false |
| Using && | false |

## Related functions

Other related functions are:

- [[NOT]]
- [[OR]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/and-function-dax](https://docs.microsoft.com/en-us/dax/and-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
