---
title: "TIMEVALUE"
function: "timevalue"
category: "Date and Time"
url: "https://dax.guide/timevalue/"
source: "dax.guide"
重要度:
难度:
---

# TIMEVALUE DAX Function (Date and Time)

Converts a time in text format to a time in datetime format.

## Syntax

TIMEVALUE ( <TimeText> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| TimeText |  | A text string that gives a time; date information in the string is ignored. |

## Return values

Scalar A single [datetime](https://dax.guide/dt/datetime/) value.

A time in datetime format.

## Remarks

The TIMEVALUE function uses the locale and date/time settings of the client computer to understand the text value when performing the conversion.

[» 1 related function](#alt)  

## Examples

```dax


--  DATEVALUE/TIMEVALUE convert strings into dates and times

EVALUATE

{

    DATEVALUE ( "10/15/2020" ),

    DATEVALUE ( "10-15-2020" ),

    TIMEVALUE ( "12:45:01" ),

    TIMEVALUE ( "12.45.01 AM" ),

    TIMEVALUE ( "15:45:01 AM" )

}

```

| Value |
| --- |
| 2020-10-15 00:00:00 |
| 2020-10-15 00:00:00 |
| 1899-12-30 12:45:01 |
| 1899-12-30 00:45:01 |
| 1899-12-30 15:45:01 |

## Related functions

Other related functions are:

- [[DATEVALUE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Imke Feldmann

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/timevalue-function-dax](https://docs.microsoft.com/en-us/dax/timevalue-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
