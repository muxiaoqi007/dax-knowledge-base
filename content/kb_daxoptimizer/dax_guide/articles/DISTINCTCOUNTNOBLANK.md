---
title: "DISTINCTCOUNTNOBLANK"
function: "distinctcountnoblank"
category: "Aggregation"
url: "https://dax.guide/distinctcountnoblank/"
source: "dax.guide"
重要度:
难度:
---

# DISTINCTCOUNTNOBLANK DAX Function (Aggregation)

Counts the number of distinct values in a column.

## Syntax

DISTINCTCOUNTNOBLANK ( <ColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName |  | The column for which the distinct values are counted, no blank value included. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

The number of distinct values in ColumnName, ignoring the blank value.

## Remarks

DISTINCTCOUNTNOBLANK is syntax sugar for evaluating [[DISTINCTCOUNT]] removing [[BLANK]] from the filter context.

The syntax:

```dax


DISTINCTCOUNTNOBLANK ( table[column] )

```

corresponds to:

```dax


CALCULATE ( 

    DISTINCTCOUNT ( table[column] ),

    KEEPFILTERS ( NOT ISBLANK ( table[column] ) )

)

```

[» 1 related article](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--

--  DISTINCTCOUNT considers BLANK as a valid value, whereas

--  DISTINCTCOUNTNOBLANK does not count any blank value

-- 

DEFINE

    MEASURE Customer[# Stores] =

        COUNTROWS ( Store )

    MEASURE Customer[# Manager] =

        DISTINCTCOUNT ( Store[Area Manager] )

    MEASURE Customer[# Manager (no blank)] =

        DISTINCTCOUNTNOBLANK ( Store[Area Manager] )

    MEASURE Customer[# Stores without manager] =

        COUNTBLANK ( Store[Area Manager] )

EVALUATE

SUMMARIZECOLUMNS ( 

    Store[Continent],

    "# Stores", [# Stores],

    "# Manager", [# Manager],

    "# Manager (no blank)", [# Manager (no blank)],

    "# Stores without manager", [# Stores without manager]

)

ORDER BY

    Store[Continent]

```

| Continent | # Stores | # Manager | # Manager (no blank) | # Stores without manager |
| --- | --- | --- | --- | --- |
| Asia | 41 | 5 | 5 | (Blank) |
| Europe | 54 | 8 | 7 | 7 |
| North America | 209 | 40 | 39 | 5 |

More examples available for the DISTINCTCOUNT function.

## Related articles

Learn more about DISTINCTCOUNTNOBLANK in the following articles:

- [**Controlling drillthrough in Excel PivotTables connected to Power BI or Analysis Services**](https://www.sqlbi.com/articles/controlling-drillthrough-in-excel-pivottables-connected-to-power-bi-or-analysis-services/)

  This article describes how to customize the drillthrough experience in Excel PivotTables connected to Power BI datasets or Analysis Services databases. [» Read more](https://www.sqlbi.com/articles/controlling-drillthrough-in-excel-pivottables-connected-to-power-bi-or-analysis-services/)

## Related functions

Other related functions are:

- [[APPROXIMATEDISTINCTCOUNT]]
- [[DISTINCTCOUNT]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Mads Hannibal, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/distinctcountnoblank-function-dax](https://docs.microsoft.com/en-us/dax/distinctcountnoblank-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
