---
title: "ROLLUPADDISSUBTOTAL"
function: "rollupaddissubtotal"
category: "Table manipulation"
url: "https://dax.guide/rollupaddissubtotal/"
source: "dax.guide"
重要度:
难度:
---

# ROLLUPADDISSUBTOTAL DAX Function (Table manipulation)

Identifies a subset of columns specified in the call to [[SUMMARIZECOLUMNS]] function that should be used to calculate groups of subtotals.

## Syntax

ROLLUPADDISSUBTOTAL ( [<GrandtotalFilter>], <GroupBy\_ColumnName>, <Name> [, [<GroupLevelFilter>] [, <GroupBy\_ColumnName>, <Name> [, [<GroupLevelFilter>] [, … ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| GrandtotalFilter | Optional | Filter to be applied to the grandtotal level. |
| GroupBy\_ColumnName | Repeatable | A column to be returned. |
| Name | Repeatable | A column name to be added. |
| GroupLevelFilter | Optional Repeatable | Filter to be applied to the current level. |

## Return values

The function does not return a value. It only specifies the set of columns to be subtotaled.

## Remarks

The ROLLUPADDISSUBTOTAL function is used exclusively within [[SUMMARIZECOLUMNS]].

The addition of the ROLLUPADDISSUBTOTAL() syntax modifies the behavior of the SUMMARIZECOUMNS function by adding roll-up/subtotal rows to the result based on the groupBy\_columnName columns.

[» 3 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  ROLLUPADDISSUBTOTAL computes subtotals for the specified

--  columns and adds a new column to the result indicating 

--  whether the current row is a subtotal or not.

EVALUATE

SUMMARIZECOLUMNS (

    ROLLUPADDISSUBTOTAL ( 'Date'[Calendar Year], "Year total" ),

    TREATAS ( { "CY 2008", "CY 2009" }, 'Date'[Calendar Year] ),

    TREATAS ( { "Bachelors", "Partial College" }, Customer[Education] ),

    "Amount", [Sales Amount]

)

ORDER BY [Year total], [Calendar Year]

```

| Calendar Year | Year total | Amount |
| --- | --- | --- |
| 2008-01-01 | false | 747,365.53 |
| 2009-01-01 | false | 362,354.56 |
| (Blank) | true | 1,109,720.09 |

```dax


--  You can specify multiple ROLLUPADDISSUBTOTAL in the same query

--  to produce matrices of subtotals.

EVALUATE

SUMMARIZECOLUMNS (

    ROLLUPADDISSUBTOTAL ( 'Date'[Calendar Year], "Year total" ),

    ROLLUPADDISSUBTOTAL ( Customer[Education],   "Education total" ),

    TREATAS ( { "CY 2008", "CY 2009" }, 'Date'[Calendar Year] ),

    TREATAS ( { "Bachelors", "Partial College" }, Customer[Education] ),

    "Amount", [Sales Amount]

)

ORDER BY [Year total], [Calendar Year], [Education total], [Education]

```

| Calendar Year | Education | Year total | Education total | Amount |
| --- | --- | --- | --- | --- |
| 2008-01-01 | Bachelors | false | false | 429,554.13 |
| 2008-01-01 | Partial College | false | false | 317,811.40 |
| 2008-01-01 | (Blank) | false | true | 747,365.53 |
| 2009-01-01 | Bachelors | false | false | 189,037.54 |
| 2009-01-01 | Partial College | false | false | 173,317.03 |
| 2009-01-01 | (Blank) | false | true | 362,354.56 |
| (Blank) | Bachelors | true | false | 618,591.67 |
| (Blank) | Partial College | true | false | 491,128.43 |
| (Blank) | (Blank) | true | true | 1,109,720.09 |

```dax


--  Alternatively, you can use multiple ROLLUP columns in the same

--  ROLLUPADDISSUBTOTAL to reduce the number of subtotals generated

--  including only the group totals at higher levels following the 

--  hierarchy of columns specified.



-- The first result has subtotals by Year and then Education (no Education total)

EVALUATE

SUMMARIZECOLUMNS (

    ROLLUPADDISSUBTOTAL ( 

        'Date'[Calendar Year], "Year total",

        Customer[Education],   "Education total" 

    ),

    TREATAS ( { "CY 2008", "CY 2009" }, 'Date'[Calendar Year] ),

    TREATAS ( { "Bachelors", "Partial College" }, Customer[Education] ),

    "Amount", [Sales Amount]

)

ORDER BY [Year total], [Calendar Year], [Education total], [Education]



-- The second result has subtotals by Education and then Year (no Year total)

EVALUATE

SUMMARIZECOLUMNS (

    ROLLUPADDISSUBTOTAL ( 

        Customer[Education],   "Education total",

        'Date'[Calendar Year], "Year total"

    ),

    TREATAS ( { "CY 2008", "CY 2009" }, 'Date'[Calendar Year] ),

    TREATAS ( { "Bachelors", "Partial College" }, Customer[Education] ),

    "Amount", [Sales Amount]

)

ORDER BY [Year total], [Education], [Calendar Year]

```

| Calendar Year | Education | Year total | Education total | Amount |
| --- | --- | --- | --- | --- |
| 2008-01-01 | Bachelors | false | false | 429,554.13 |
| 2008-01-01 | Partial College | false | false | 317,811.40 |
| 2008-01-01 | (Blank) | false | true | 747,365.53 |
| 2009-01-01 | Bachelors | false | false | 189,037.54 |
| 2009-01-01 | Partial College | false | false | 173,317.03 |
| 2009-01-01 | (Blank) | false | true | 362,354.56 |
| (Blank) | (Blank) | true | true | 1,109,720.09 |

| Education | Calendar Year | Education total | Year total | Amount |
| --- | --- | --- | --- | --- |
| Bachelors | 2008-01-01 | false | false | 429,554.13 |
| Bachelors | 2009-01-01 | false | false | 189,037.54 |
| Partial College | 2008-01-01 | false | false | 317,811.40 |
| Partial College | 2009-01-01 | false | false | 173,317.03 |
| (Blank) | (Blank) | true | true | 1,109,720.09 |
| Bachelors | (Blank) | false | true | 618,591.67 |
| Partial College | (Blank) | false | true | 491,128.43 |

```dax


--  Using ROLLUPGROUP you can further reduce the number of subtotals

--  by grouping together several columns.

EVALUATE

SUMMARIZECOLUMNS (

    ROLLUPADDISSUBTOTAL (

        ROLLUPGROUP ( 'Date'[Calendar Year], Customer[Education] ),

        "Year total"

    ),

    TREATAS ( { "CY 2008", "CY 2009" }, 'Date'[Calendar Year] ),

    TREATAS ( { "Bachelors", "Partial College" }, Customer[Education] ),

    "Amount", [Sales Amount]

)

ORDER BY

    [Year total],

    [Calendar Year],

    [Education]

```

| Calendar Year | Education | Year total | Amount |
| --- | --- | --- | --- |
| 2008-01-01 | Bachelors | false | 429,554.13 |
| 2008-01-01 | Partial College | false | 317,811.40 |
| 2009-01-01 | Bachelors | false | 189,037.54 |
| 2009-01-01 | Partial College | false | 173,317.03 |
| (Blank) | (Blank) | true | 1,109,720.09 |

## Related articles

Learn more about ROLLUPADDISSUBTOTAL in the following articles:

- [**Introducing SUMMARIZECOLUMNS**](https://www.sqlbi.com/articles/introducing-summarizecolumns/)

  This article explains how to use SUMMARIZECOLUMNS, which is a replacement of SUMMARIZE and does not require the use of ADDCOLUMNS to obtain good performance. [» Read more](https://www.sqlbi.com/articles/introducing-summarizecolumns/)
- [**Introducing VISUAL SHAPE for visual calculations in Power BI**](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/)

  This article introduces the VISUAL SHAPE clause, which defines a hierarchical structure for a table used in visual calculations. [» Read more](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/)
- [**Understanding value filter behavior in SUMMARIZECOLUMNS**](https://www.sqlbi.com/articles/understanding-value-filter-behavior-in-summarizecolumns/)

  Value filter behavior is a setting in Power BI semantic models that controls how filters are combined in SUMMARIZECOLUMNS. This article explains how it works and suggests its best configuration. [» Read more](https://www.sqlbi.com/articles/understanding-value-filter-behavior-in-summarizecolumns/)

## Related functions

Other related functions are:

- [[ROLLUPISSUBTOTAL]]
- [[SUMMARIZECOLUMNS]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/rollupaddissubtotal-function-dax](https://docs.microsoft.com/en-us/dax/rollupaddissubtotal-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
