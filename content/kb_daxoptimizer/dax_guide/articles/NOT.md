---
title: "NOT"
function: "not"
category: "Logical"
url: "https://dax.guide/not/"
source: "dax.guide"
重要度:
难度:
---

# NOT DAX Function (Logical)

Changes FALSE to TRUE, or TRUE to FALSE.

## Syntax

NOT ( <Logical> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Logical |  | A value or expression that can be evaluated to TRUE or FALSE. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

TRUE or FALSE

## Remarks

NOT is not a real function; it is an [operator](https://dax.guide/op/not/) that can be used as a function because of the equivalent syntax.

[» 3 related functions](#alt)  

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

The following example has the purpose of clarifying that NOT is an operator and not a function.

```dax


DEFINE

    TABLE 'NOT IN' =

        ADDCOLUMNS (

            CROSSJOIN (

                { FALSE, TRUE },

                SELECTCOLUMNS ( { 0, 1, 2 }, "Expression #", [Value] ),

                SELECTCOLUMNS (

                    { ( FALSE, FALSE ), ( FALSE, TRUE ), ( TRUE, TRUE ) },

                    "List Item 1", [Value1],

                    "List Item 2", [Value2]

                )

            ),

            "Expression",

                SWITCH (

                    [Expression #],

                    0,  "NOT ( " & [Value] & " ) IN …",

                    1,  "NOT ( " & [Value] & " IN … )",

                    2,  "( NOT " & [Value] & " ) IN …"

                ),

            "Result",

                SWITCH (

                    [Expression #],

                    0, NOT ( [Value] ) IN { [List Item 1], [List Item 2] },

                    1, NOT ( [Value] IN { [List Item 1], [List Item 2] } ),

                    2, ( NOT [Value] ) IN { [List Item 1], [List Item 2] }

                )

        )



EVALUATE

SELECTCOLUMNS (

    VALUES ( 'NOT IN'[Expression] ),

    "Expression", 'NOT IN'[Expression],

    "…{ FALSE, FALSE }",

        CALCULATE (

            VALUES ( 'NOT IN'[Result] ),

            'NOT IN'[List Item 1] = FALSE,

            'NOT IN'[List Item 2] = FALSE

        ),

    "…{ TRUE, TRUE }",

        CALCULATE (

            VALUES ( 'NOT IN'[Result] ),

            'NOT IN'[List Item 1] = TRUE,

            'NOT IN'[List Item 2] = TRUE

        ),

    "…{ FALSE, TRUE }",

        CALCULATE (

            VALUES ( 'NOT IN'[Result] ),

            'NOT IN'[List Item 1] = FALSE,

            'NOT IN'[List Item 2] = TRUE

        )

)

ORDER BY […{ FALSE, FALSE }] ASC



```

| Expression | …{ FALSE, FALSE } | …{ TRUE, TRUE } | …{ FALSE, TRUE } |
| --- | --- | --- | --- |
| NOT ( FALSE ) IN … | false | true | false |
| NOT ( FALSE IN … ) | false | true | false |
| ( NOT FALSE ) IN … | false | true | true |
| NOT ( TRUE ) IN … | true | false | false |
| NOT ( TRUE IN … ) | true | false | false |
| ( NOT TRUE ) IN … | true | false | true |

## Related functions

Other related functions are:

- [[FALSE]]
- [[IF]]
- [[TRUE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/not-function-dax](https://docs.microsoft.com/en-us/dax/not-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
