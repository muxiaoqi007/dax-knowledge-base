---
title: "FALSE"
function: "false"
category: "Logical"
url: "https://dax.guide/false/"
source: "dax.guide"
重要度:
难度:
---

# FALSE DAX Function (Logical)

Returns the logical value FALSE.

## Syntax

FALSE ( )

This expression has no parameters.

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

Always FALSE.

## Remarks

Writing FALSE() or FALSE produces the same result.  
The FALSE value is converted to 0 as decimal or integer.

[» 1 related function](#alt)  

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

## Related functions

Other related functions are:

- [[TRUE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/false-function-dax](https://docs.microsoft.com/en-us/dax/false-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
