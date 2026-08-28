---
title: "GEOMEAN"
function: "geomean"
category: "Statistical"
url: "https://dax.guide/geomean/"
source: "dax.guide"
重要度:
难度:
---

# GEOMEAN DAX Function (Statistical)

Returns geometric mean of given column reference.

## Syntax

GEOMEAN ( <ColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName |  | Column that contains values for geometric mean. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Return a decimal number with the geometric mean.

## Remarks

The GEOMEAN function internally executes [[GEOMEANX]], without any performance difference.  
The following GEOMEAN call:

```dax


GEOMEAN ( table[column] )

```

corresponds to the following [[GEOMEANX]] call:

```dax


GEOMEANX (

    table,

    table[column]

)

```

[» 1 related function](#alt)  

## Examples

```dax


--  GEOMEAN computes the geometric mean of the values in a column.

--  GEOMEAN is the short version of GEOMEANX, when used with one 

--  column only.

DEFINE

    TABLE SampleData = { 2, 4, 4, 4, 5, 5, 7, 9 }

EVALUATE

{

     ( "Average",        AVERAGE ( SampleData[Value] ) ),

     ( "Geometric Mean", GEOMEAN ( SampleData[Value] ) )

}

```

| Value1 | Value2 |
| --- | --- |
| Average | 5.00 |
| Geometric Mean | 4.60 |

## Related functions

Other related functions are:

- [[GEOMEANX]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/geomean-function-dax](https://docs.microsoft.com/en-us/dax/geomean-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
