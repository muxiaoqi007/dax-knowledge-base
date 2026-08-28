---
title: "SECOND"
function: "second"
category: "Date and Time"
url: "https://dax.guide/second/"
source: "dax.guide"
重要度:
难度:
---

# SECOND DAX Function (Date and Time)

Returns a number from 0 to 59 representing the second.

## Syntax

SECOND ( <Datetime> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Datetime |  | The time in datetime format or text in time format, such as 16:48:23 or 4:48:47 PM. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

An integer number from 0 to 59.

## Remarks

When the datetime argument is a text representation of the date and time, the function uses the locale and date/time settings of the client computer to understand the text value in order to perform the conversion. Most locales use the colon (:) as the time separator and any input text using colons as time separators will parse correctly. Verify your locale settings to understand your results.

[» 2 related functions](#alt)  

## Examples

```dax


--  Extract time parts from a DateTime column

EVALUATE

ADDCOLUMNS (

    TOPN ( 10, VALUES ( Sales[Order Time] ) ),

    "Hour",   HOUR   ( Sales[Order Time] ),

    "Minute", MINUTE ( Sales[Order Time] ),

    "Second", SECOND ( Sales[Order Time] )

)

```

| Order Time | Hour | Minute | Second |
| --- | --- | --- | --- |
| 01:14:19 | 1 | 14 | 19 |
| 02:14:19 | 2 | 14 | 19 |
| 03:14:19 | 3 | 14 | 19 |
| 04:14:19 | 4 | 14 | 19 |
| 06:14:19 | 6 | 14 | 19 |
| 07:14:19 | 7 | 14 | 19 |
| 08:14:19 | 8 | 14 | 19 |
| 09:14:19 | 9 | 14 | 19 |
| 11:14:19 | 11 | 14 | 19 |
| 12:14:19 | 12 | 14 | 19 |

## Related functions

Other related functions are:

- [[HOUR]]
- [[MINUTE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/second-function-dax](https://docs.microsoft.com/en-us/dax/second-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
