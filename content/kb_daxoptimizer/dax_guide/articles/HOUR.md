---
title: "HOUR"
function: "hour"
category: "Date and Time"
url: "https://dax.guide/hour/"
source: "dax.guide"
重要度:
难度:
---

# HOUR DAX Function (Date and Time)

Returns the hour as a number from 0 (12:00 A.M.) to 23 (11:00 P.M.).

## Syntax

HOUR ( <Datetime> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Datetime |  | A datetime value or text in time format, such as 16:48:00 or 4:48:00 PM. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

An integer number from 0 to 23.

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

- [[MINUTE]]
- [[SECOND]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/hour-function-dax](https://docs.microsoft.com/en-us/dax/hour-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
