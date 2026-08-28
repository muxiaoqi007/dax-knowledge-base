---
title: "DATEVALUE"
function: "datevalue"
category: "Date and Time"
url: "https://dax.guide/datevalue/"
source: "dax.guide"
重要度:
难度:
---

# DATEVALUE DAX Function (Date and Time)

Converts a date in the form of text to a date in datetime format.

## Syntax

DATEVALUE ( <DateText> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| DateText |  | A text string that represents a date. |

## Return values

Scalar A single [datetime](https://dax.guide/dt/datetime/) value.

A date in datetime format.

## Remarks

The DATEVALUE function uses the locale settings of the model to understand the text value when performing the conversion. If the locale settings of the model represent dates in the format of Month/Day/Year, then the string “1/8/2009” would be converted to a datetime value equivalent to January 8th of 2009. However, if the current date and time settings represent dates in the format of Day/Month/Year, the same string would be converted as a datetime value equivalent to August 1st of 2009. An heuristic fall back to alternative formats rather than raising an error if the format provided by the locale settings of the model does not work. Therefore, a string like “1/31/2009” will always return the January 31st of 2009 regardless of the locale settings.

If the year portion of the DateText argument is omitted, the DATEVALUE function uses the current year from your computer’s built-in clock. Time information in the DateText argument is ignored.

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

```dax


--  If month and day are exchanged, the conversion still works

--  The default is defined by the international settings

EVALUATE

{

    DATEVALUE ( "28/02/2020" ),

    DATEVALUE ( "02/28/2020" ),

    DATEVALUE ( "08/10/2020" ),   -- 10th of August

    DATEVALUE ( "2020-02-06" )

}

```

| Value |
| --- |
| 2020-02-28 |
| 2020-02-28 |
| 2020-08-10 |
| 2020-02-06 |

## Related functions

Other related functions are:

- [[TIMEVALUE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/datevalue-function-dax](https://docs.microsoft.com/en-us/dax/datevalue-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
