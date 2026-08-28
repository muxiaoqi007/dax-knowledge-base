---
title: "YEARFRAC"
function: "yearfrac"
category: "Date and Time"
url: "https://dax.guide/yearfrac/"
source: "dax.guide"
重要度:
难度:
---

# YEARFRAC DAX Function (Date and Time)

Returns the year fraction representing the number of whole days between start\_date and end\_date.

## Syntax

YEARFRAC ( <StartDate>, <EndDate> [, <Basis>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| StartDate |  | The start date in datetime format. |
| EndDate |  | The end date in datetime format. |
| Basis | Optional | The type of day count basis to use.  0 (default) : US (NASD) 30/360  1 : Actual/actual  2 : Actual/360  3 : Actual/365  4 : European 30/360 |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Fraction of the year.

## Remarks

If the argument is a string, it is translated into a DateTime value using the same rules applied by the [[DATEVALUE]] function.

YEARFRAC can be used to compute the current age of a customer based on the difference between the current day and the customer’s birthdate, but because of a bug, it is suggested to use another technique (also faster) based on quotient and floor, as described in related content.

[» 1 related article](#articles)  
[» 3 related functions](#alt)  

## Examples

```dax


--  DATEDIFF computes the delta between two dates, using different units of measure

--  YEAFRAC returns the delta as a fraction (in years) 

EVALUATE

VAR StartDate =  DATE ( 2011, 01, 01 )

VAR EndDate =    DATE ( 2012, 12, 15 )

RETURN

    { 

        ( "DATEDIFF Year",     DATEDIFF ( StartDate, EndDate, YEAR ) ),

        ( "DATEDIFF Quarter",  DATEDIFF ( StartDate, EndDate, QUARTER ) ),

        ( "DATEDIFF Month",    DATEDIFF ( StartDate, EndDate, MONTH ) ),

        ( "DATEDIFF Day",      DATEDIFF ( StartDate, EndDate, DAY ) ),

        ( "Subtraction",       INT ( EndDate - StartDate ) ),

        ( "YEARFRAC",          YEARFRAC ( StartDate, EndDate ) )

    }    

```

| Value1 | Value2 |
| --- | --- |
| DATEDIFF Year | 1.00 |
| DATEDIFF Quarter | 7.00 |
| DATEDIFF Month | 23.00 |
| DATEDIFF Day | 714.00 |
| Subtraction | 714.00 |
| YEARFRAC | 1.96 |

```dax


--  The default of YEARFRAC is "US 30/360"

EVALUATE

VAR StartDate =  DATE ( 2010, 01, 01 )

VAR EndDate =    DATE ( 2011, 12, 15 )

RETURN

    { 

        ( "YEARFRAC",       YEARFRAC ( StartDate, EndDate ) ),

        ( "Number of days", INT ( EndDate - StartDate ) ),

        ( "YEARFRAC *365",  YEARFRAC ( StartDate, EndDate ) * 365 )

    }  

```

| Value1 | Value2 |
| --- | --- |
| YEARFRAC | 1.96 |
| Number of days | 713.00 |
| YEARFRAC \*365 | 713.78 |

```dax


--  Different standards produce different fractions

--  YEAFRAC is intended as a financial function, following the required

--  standard of 30/360.

EVALUATE

VAR StartDate =  DATE ( 2011, 01, 01 )

VAR EndDate =    DATE ( 2011, 12, 15 )

RETURN

    { 

        ( "YEARFRAC US 30/360",       FORMAT ( YEARFRAC ( StartDate, EndDate, 0 ), "0.0000" ) ),

        ( "YEARFRAC Actual / Actual", FORMAT ( YEARFRAC ( StartDate, EndDate, 1 ), "0.0000" ) ),

        ( "YEARFRAC Actual / 360",    FORMAT ( YEARFRAC ( StartDate, EndDate, 2 ), "0.0000" ) ),

        ( "YEARFRAC Actual / 365",    FORMAT ( YEARFRAC ( StartDate, EndDate, 3 ), "0.0000" ) ),

        ( "YEARFRAC EU 30/360",       FORMAT ( YEARFRAC ( StartDate, EndDate, 4 ), "0.0000" ) )

    }    

```

| Value1 | Value2 |
| --- | --- |
| YEARFRAC US 30/360 | 0.9556 |
| YEARFRAC Actual / Actual | 0.9534 |
| YEARFRAC Actual / 360 | 0.9667 |
| YEARFRAC Actual / 365 | 0.9534 |
| YEARFRAC EU 30/360 | 0.9556 |

## Related articles

Learn more about YEARFRAC in the following articles:

- [**Correct calculate of age in DAX from birthday**](https://www.sqlbi.com/blog/marco/2018/06/24/correct-calculate-of-age-in-dax-from-birthday/)

  Consider alternative to YEARFRAC in order to get the right age based on birthday because of bugs in YEARFRAC. [» Read more](https://www.sqlbi.com/blog/marco/2018/06/24/correct-calculate-of-age-in-dax-from-birthday/)

## Related functions

Other related functions are:

- [[WEEKDAY]]
- [[WEEKNUM]]
- [[QUARTER]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/yearfrac-function-dax](https://docs.microsoft.com/en-us/dax/yearfrac-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
