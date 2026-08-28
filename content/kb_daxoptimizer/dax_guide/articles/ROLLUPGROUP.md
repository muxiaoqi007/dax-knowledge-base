---
title: "ROLLUPGROUP"
function: "rollupgroup"
category: "Table manipulation"
url: "https://dax.guide/rollupgroup/"
source: "dax.guide"
重要度:
难度:
---

# ROLLUPGROUP DAX Function (Table manipulation)

Identifies a subset of columns specified in the call to [[SUMMARIZE]] or [[SUMMARIZECOLUMNS]] function that should be used to calculate groups of subtotals.

## Syntax

ROLLUPGROUP ( <GroupBy\_ColumnName> [, <GroupBy\_ColumnName> [, … ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| GroupBy\_ColumnName | Repeatable | A column to be returned. |

## Return values

The function does not return a value. It only specifies the set of columns to be subtotaled.

## Remarks

The [[ROLLUP]] function is used exclusively within [[SUMMARIZE]], [[SUMMARIZECOLUMNS]], or [[ADDMISSINGITEMS]].

ROLLUPGROUP can be used to calculate groups of subtotals. If used within [[SUMMARIZE]] in-place of [[ROLLUP]], ROLLUPGROUP will yield the same result by adding roll-up rows to the result on the GroupBy\_ColumnName columns. However, the addition of ROLLUPGROUP() inside a [[ROLLUP]] syntax can be used to prevent partial subtotals in roll-up rows.

[» 1 related article](#articles)  
[» 3 related functions](#alt)  

## Examples

```dax


--  Using ROLLUPGROUP in SUMMARIZE you can reduce the number 

--  of subtotals by grouping together several columns.

EVALUATE

CALCULATETABLE (

    SUMMARIZE (

        Sales,

        ROLLUP ( ROLLUPGROUP ( 'Date'[Calendar Year], Customer[Education] ) ),

        "Year total", ISSUBTOTAL ( 'Date'[Calendar Year] ),

        "Education total", ISSUBTOTAL ( Customer[Education] ),

        "Amount", [Sales Amount]

    ),

    TREATAS ( { "CY 2008", "CY 2009" }, 'Date'[Calendar Year] ),

    TREATAS ( { "Bachelors", "Partial College" }, Customer[Education] )

)

ORDER BY

    [Year total],

    [Calendar Year]

```

| Calendar Year | Education | Year total | Education total | Amount |
| --- | --- | --- | --- | --- |
| 2008-01-01 | Bachelors | false | false | 429,554.13 |
| 2008-01-01 | Partial College | false | false | 317,811.40 |
| 2009-01-01 | Bachelors | false | false | 189,037.54 |
| 2009-01-01 | Partial College | false | false | 173,317.03 |
| (Blank) | (Blank) | true | true | 1,109,720.09 |

```dax


--  ROLLUPGROUP can also be used in SUMMARIZECOLUMNS with the same

--  goal: grouping columns together to reduce the number of subtotals.

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

Learn more about ROLLUPGROUP in the following articles:

- [**Understanding value filter behavior in SUMMARIZECOLUMNS**](https://www.sqlbi.com/articles/understanding-value-filter-behavior-in-summarizecolumns/)

  Value filter behavior is a setting in Power BI semantic models that controls how filters are combined in SUMMARIZECOLUMNS. This article explains how it works and suggests its best configuration. [» Read more](https://www.sqlbi.com/articles/understanding-value-filter-behavior-in-summarizecolumns/)

## Related functions

Other related functions are:

- [[ROLLUP]]
- [[SUMMARIZE]]
- [[ROLLUPISSUBTOTAL]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/rollupgroup-function-dax](https://docs.microsoft.com/en-us/dax/rollupgroup-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
