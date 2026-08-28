---
title: "ISEVEN"
function: "iseven"
category: "Information"
url: "https://dax.guide/iseven/"
source: "dax.guide"
重要度:
难度:
---

# ISEVEN DAX Function (Information)

Returns TRUE if number is even, or FALSE if number is odd.

## Syntax

ISEVEN ( <Number> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | The value to test. If number is not an integer, it is rounded to the nearest integer. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

Returns TRUE if Number is even, or FALSE if number is odd.

[» 1 related function](#alt)  

## Examples

```dax


--  ISEVEN checks that a number is EVEN,

--  ISODD checks that a number is ODD

EVALUATE

    {

       ( "ISEVEN ( 10 )", ISEVEN ( 10 ) ),

       ( "ISODD  ( 10 )", ISODD  ( 10 ) ),

       ( "ISEVEN ( 11 )", ISEVEN ( 11 ) ),

       ( "ISODD  ( 11 )", ISODD  ( 11 ) ),

       ( "ISEVEN ( -3 )", ISEVEN ( -3 ) ),

       ( "ISODD  ( -3 )", ISODD  ( -3 ) ),

       ( "ISEVEN ( -4 )", ISEVEN ( -4 ) ),

       ( "ISODD  ( -4 )", ISODD  ( -4 ) )

    }

```

| Value1 | Value2 |
| --- | --- |
| ISEVEN ( 10 ) | true |
| ISODD  ( 10 ) | false |
| ISEVEN ( 11 ) | false |
| ISODD  ( 11 ) | true |
| ISEVEN ( -3 ) | false |
| ISODD  ( -3 ) | true |
| ISEVEN ( -4 ) | true |
| ISODD  ( -4 ) | false |

## Related functions

Other related functions are:

- [[ISODD]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/iseven-function-dax](https://docs.microsoft.com/en-us/dax/iseven-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
