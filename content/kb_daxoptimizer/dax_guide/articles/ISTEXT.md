---
title: "ISTEXT"
function: "istext"
category: "Information"
url: "https://dax.guide/istext/"
source: "dax.guide"
重要度:
难度:
---

# ISTEXT DAX Function (Information)

Checks whether a value is text, and returns TRUE or FALSE. This is an alias of [[ISSTRING]].

## Syntax

ISTEXT ( <Value> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Value |  | The value you want to test. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

TRUE if the value is text; otherwise FALSE.

## Remarks

When applied to a column reference as expression, this functions tests the data type of the column, returning TRUE whether the column is a string data type and FALSE for any other data type.

[» 3 related functions](#alt)  

## Examples

```dax


--  ISLOGICAL, ISTEXT, ISNONTEXT and ISNUMBER check their argument 

--  for the required data type.

--

--  Different results with strings, numbers, Booleans, and BLANK

EVALUATE

VAR _Logical = TRUE

VAR Number  = -1.2

VAR Txt     = "SQLBI"

VAR ValueTable =

    {

        ( "ISLOGICAL(Value)", ISLOGICAL ( _Logical ), ISLOGICAL ( Number ), ISLOGICAL ( Txt ), ISLOGICAL ( BLANK () ) ),

        ( "ISNUMBER (Value)", ISNUMBER ( _Logical ), ISNUMBER ( Number ), ISNUMBER ( Txt ), ISNUMBER ( BLANK () ) ),

        ( "ISTEXT(Value)", ISTEXT ( _Logical ), ISTEXT ( Number ), ISTEXT ( Txt ), ISTEXT ( BLANK () ) ),

        ( "ISNONTEXT(Value)", ISNONTEXT ( _Logical ), ISNONTEXT ( Number ), ISNONTEXT ( Txt ), ISNONTEXT ( BLANK () ) )

    }

RETURN 

    SELECTCOLUMNS(

        ValueTable,

        "Function Call      VALUE = ",[Value1],

        "TRUE",       [Value2],

        "-1.2",       [Value3],

        """SQLBI""",  [Value4],

        "BLANK()",    [Value5]

    )

```

| Function Call      VALUE = | TRUE | -1.2 | “SQLBI” | BLANK() |
| --- | --- | --- | --- | --- |
| ISLOGICAL(Value) | true | false | false | false |
| ISNUMBER (Value) | false | true | false | false |
| ISTEXT(Value) | false | false | true | false |
| ISNONTEXT(Value) | true | true | false | true |

```dax


--  ISLOGICAL, ISTEXT, ISNONTEXT and ISNUMBER check their argument 

--  for the required data type.

--

--  Different results with strings, numbers, booleans, BLANK

EVALUATE

VAR ValueToCheck = "SQLBI"

RETURN

{

    ( "ISLOGICAL (" & ValueToCheck & ")" , ISLOGICAL ( ValueToCheck )),

    ( "ISTEXT    (" & ValueToCheck & ")" , ISTEXT    ( ValueToCheck )),

    ( "ISNONTEXT (" & ValueToCheck & ")" , ISNONTEXT ( ValueToCheck )),

    ( "ISNUMBER  (" & ValueToCheck & ")" , ISNUMBER  ( ValueToCheck ))

}



```

| Value1 | Value2 |
| --- | --- |
| ISLOGICAL (SQLBI) | false |
| ISTEXT (SQLBI) | true |
| ISNONTEXT (SQLBI) | false |
| ISNUMBER (SQLBI) | false |

## Related functions

Other related functions are:

- [[ISLOGICAL]]
- [[ISNONTEXT]]
- [[ISNUMBER]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/istext-function-dax](https://docs.microsoft.com/en-us/dax/istext-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
