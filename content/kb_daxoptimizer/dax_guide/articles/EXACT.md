---
title: "EXACT"
function: "exact"
category: "Text"
url: "https://dax.guide/exact/"
source: "dax.guide"
重要度:
难度:
---

# EXACT DAX Function (Text)

Checks whether two text strings are exactly the same, and returns TRUE or FALSE. EXACT is case-sensitive.

## Syntax

EXACT ( <Text1>, <Text2> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Text1 |  | The first text string. |
| Text2 |  | The second text string. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

## Remarks

An empty string and [[BLANK]] are considered the same value.

## Examples

```dax


--  EXACT performs string comparison in a case-sensitive way

--  By default, DAX is not case sensitive in comparison. 

--  EXACT can be useful to force case-sensitivity.

DEFINE

VAR A = "sqlbi"

VAR B = "SQLBI"

EVALUATE

    {

        ( "A = B", A = B ),

        ( "EXACT ( A, B )", EXACT ( A, B ) )

    }

```

| Value1 | Value2 |
| --- | --- |
| A = B | true |
| EXACT ( A, B ) | false |

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/exact-function-dax](https://docs.microsoft.com/en-us/dax/exact-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
