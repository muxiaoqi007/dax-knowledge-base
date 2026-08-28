---
title: "VALUE"
function: "value"
category: "Text"
url: "https://dax.guide/value/"
source: "dax.guide"
重要度:
难度:
---

# VALUE DAX Function (Text)

Converts a text string that represents a number to a number.

## Syntax

VALUE ( <Text> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Text |  | The text to be converted. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

The converted number in decimal data type.

## Remarks

The value passed as the text parameter can be in any of the constant, number, date, or time formats recognized by DAX. If text is not in one of these formats, an error is returned.

VALUE is not common because DAX implicitly converts text to numbers as necessary.

VALUE raises an error if the conversion is not possible. In case VALUE is executed iterating a column that might contain invalid values, filtering the table may not be enough because the Vertipaq Engine that does not that VALUE is called after applying the filter. To guarantee VALUE is called for valid values, there should be an [[IF]] statement to protect the code.  
For example, the following two examples might raise an error:

```dax


SUMX ( 

    FILTER (

        'Data',

        'Data'[Type] = "Number"

    ),

    VALUE ( 'Data'[Value] )

)

```

```dax


CALCULATE (

   SUMX ( 

        'Data',

        VALUE ( 'Data'[Value] )

    ),

    'Data'[Type] = "Number"

)

```

A safe version of the two previous examples is the following:

```dax


SUMX ( 

    FILTER (

        'Data',

        'Data'[Type] = "Number"

    ),

    IF ( 

        'Data'[Type] = "Number",

        VALUE ( 'Data'[Value] )

    )

)

```

```dax


CALCULATE (

   SUMX ( 

        'Data',

        IF ( 

            'Data'[Type] = "Number",

            VALUE ( 'Data'[Value] )

        )

    ),

    'Data'[Type] = "Number"

)

```

Aggregation expressions should not assume that the filter is applied first; instead, they should ensure their own safety during execution.  
This does not implies that aggregation expressions will be evaluated during execution while ignoring the filter, which would be a performance concern.  
However, the VALUE expression may be evaluated during the Vertipaq Engine’s preparation phase against some values without the filter being applied.

## Examples

```dax


--  VALUE converts a text string that represents a number to a number.

EVALUATE

{

    ( "INT",            123,                     VALUE ( "123"        ) ),

    ( "FLOAT",          123.45678,               VALUE ( "123.45678"  ) ),

    ( "Scientific (1)", 123e3,                   VALUE ( "123e3"      ) ),

    ( "Scientific (2)", 123e-3,                  VALUE ( "123e-3"     ) ),

    ( "Date (1)",       DATE ( 2020, 10, 23 ),   VALUE ( "2020-10-23" ) ),

    ( "Date (2)",       DATE ( 2020, 10, 23 ),   VALUE ( "10/23/2020" ) ),

    ( "Date (3)",       DATE ( 2020, 10, 23 ),   VALUE ( "23/10/2020" ) ),

    ( "Time (1)",       TIME ( 10, 23, 45 ),     VALUE ( "10:23:45"   ) ),

    ( "Time (2)",       TIME ( 18, 05, 00 ),     VALUE ( "18:05:00"   ) ),

    ( "Time (3)",       TIME ( 18, 05, 00 ),     VALUE ( "6:05:00 pm" ) ),

    ( "Time (4)",       TIME ( 18, 05, 00 ),     VALUE ( "6:05pm"     ) )

}

```

| Value1 | Value2 | Value3 |
| --- | --- | --- |
| INT | 123 | 123 |
| FLOAT | 123.45678 | 123.45678 |
| Scientific (1) | 123000 | 123000 |
| Scientific (2) | 0.123 | 0.123 |
| Date (1) | 44127 | 44127 |
| Date (2) | 44127 | 44127 |
| Date (3) | 44127 | 44127 |
| Time (1) | 0.4331597222222222 | 0.4331597222222222 |
| Time (2) | 0.7534722222222222 | 0.7534722222222222 |
| Time (3) | 0.7534722222222222 | 0.7534722222222222 |
| Time (4) | 0.7534722222222222 | 0.7534722222222222 |

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Jeffrey Wang

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/value-function-dax](https://docs.microsoft.com/en-us/dax/value-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
