---
title: "FACT"
function: "fact"
category: "Math and Trig"
url: "https://dax.guide/fact/"
source: "dax.guide"
重要度:
难度:
---

# FACT DAX Function (Math and Trig)

Returns the factorial of a number, equal to 1\*2\*3\*…\* Number.

## Syntax

FACT ( <Number> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | The nonnegative number you want the factorial of. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

The factorial of the argument in a decimal data type.

## Remarks

If the number is not an integer, it is truncated and an error is returned. If the result is too large, an error is returned.

The largest factorial that can be returned by FACT is 170!

The largest factorial that can be returned by FACT without loss of precision is 21!

This function is not supported in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.

## Examples

```dax


--  FACT computes the factorial of a number

DEFINE 

    VAR Vals = GENERATESERIES ( 1, 21, 4 )

EVALUATE

ADDCOLUMNS ( 

    Vals,

    "Factorial",  FACT ( [Value] )

)



```

| Value | Factorial |
| --- | --- |
| 1 | 1 |
| 5 | 120 |
| 9 | 362,880 |
| 13 | 6,227,020,800 |
| 17 | 355,687,428,096,000 |
| 21 | 51,090,942,171,709,440,000 |

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/fact-function-dax](https://docs.microsoft.com/en-us/dax/fact-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
