---
title: "GENERATEALL"
function: "generateall"
category: "Table manipulation"
url: "https://dax.guide/generateall/"
source: "dax.guide"
重要度:
难度:
---

# GENERATEALL DAX Function (Table manipulation)

The second table expression will be evaluated for each row in the first table. Returns the crossjoin of the first table with these results, including rows for which the second table expression is empty.

## Syntax

GENERATEALL ( <Table1>, <Table2> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table1  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | The base table in Generate. || Table2  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | A table expression that will be evaluated for each row in the first table. |

## Return values

Table An entire table or a table with one or more columns.

## Remarks

- If the evaluation of *table2* for the current row in *table1* returns an empty table, then the current row from *table1* will be included in the results and columns corresponding to *table2* will have null values for that row. This is different than [[GENERATE]]() where the current row from *table1* will **not** be included in the results.
- All column names from *table1* and *table2* must be different or an error is returned.

[» 5 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  If the second argument returns an empty table, GENERATE skips the row.

--  GENERATEALL returns ALL the rows of the first argument, even 

--  though the second expression returns an empty table.

--  GENERATE is similar to CROSS APPLY in SQL

--  GENERATEALL is similar to OUTER APPLY in SQL

DEFINE

VAR Dates = 

    UNION ( 

        ROW ( "FirstDate", DATE ( 2007, 1, 6 ), "LastDate", DATE ( 2007, 1, 3 ) ),

        ROW ( "FirstDate", DATE ( 2007, 1, 9 ), "LastDate", DATE ( 2007, 1, 12 ) )

    )

VAR DatesExpanded = 

    GENERATE ( 

        Dates,

        DATESBETWEEN ( 'Date'[Date], [FirstDate], [LastDate] )

    )



VAR DatesExpandedAll = 

    GENERATEALL ( 

        Dates,

        DATESBETWEEN ( 'Date'[Date], [FirstDate], [LastDate] )

    )



EVALUATE Dates



EVALUATE DatesExpanded



EVALUATE DatesExpandedAll

    



```

| FirstDate | LastDate |
| --- | --- |
| 2007-01-06 | 2007-01-03 |
| 2007-01-09 | 2007-01-12 |

| FirstDate | LastDate | Date |
| --- | --- | --- |
| 2007-01-09 | 2007-01-12 | 2007-01-09 |
| 2007-01-09 | 2007-01-12 | 2007-01-10 |
| 2007-01-09 | 2007-01-12 | 2007-01-11 |
| 2007-01-09 | 2007-01-12 | 2007-01-12 |

| FirstDate | LastDate | Date |
| --- | --- | --- |
| 2007-01-06 | 2007-01-03 | (Blank) |
| 2007-01-09 | 2007-01-12 | 2007-01-09 |
| 2007-01-09 | 2007-01-12 | 2007-01-10 |
| 2007-01-09 | 2007-01-12 | 2007-01-11 |
| 2007-01-09 | 2007-01-12 | 2007-01-12 |

## Related articles

Learn more about GENERATEALL in the following articles:

- [**Transition Matrix Using Calculated Tables**](https://www.sqlbi.com/articles/transition-matrix-using-calculated-tables/)

  In the 2015 September update, Power BI introduced calculated tables, which are computed using DAX expressions instead of being loaded from a data source. This article shows the usage of calculated tables to solve the pattern of transition matrix for… [» Read more](https://www.sqlbi.com/articles/transition-matrix-using-calculated-tables/)
- [**Lookup multiple values in DAX**](https://www.sqlbi.com/articles/lookup-multiple-values-in-dax/)

  This article describes different techniques to retrieve multiple values from a lookup table in DAX, improving code readability and performance. [» Read more](https://www.sqlbi.com/articles/lookup-multiple-values-in-dax/)
- [**Managing hierarchical organizations in Power BI security roles**](https://www.sqlbi.com/articles/managing-hierarchical-organizations-in-power-bi-security-roles/)

  This article describes how to apply dynamic security roles in a hierarchical organization to minimize the maintenance effort on the security configuration and obtain the best performance at query time. [» Read more](https://www.sqlbi.com/articles/managing-hierarchical-organizations-in-power-bi-security-roles/)
- [**Understanding apply semantics for window functions in DAX**](https://www.sqlbi.com/articles/understanding-apply-semantics-for-window-functions-in-dax/)

  This article explains the unique behavior of apply semantics: a new way of computing table expressions when multiple rows are selected in DAX window functions. [» Read more](https://www.sqlbi.com/articles/understanding-apply-semantics-for-window-functions-in-dax/)
- [**Account receivable aging in Power BI**](https://www.sqlbi.com/articles/account-receivable-aging-in-power-bi/)

  This article describes an Accounts Receivable Aging report in Power BI, and shows how to simplify a business problem using existing modeling patterns. [» Read more](https://www.sqlbi.com/articles/account-receivable-aging-in-power-bi/)

## Related functions

Other related functions are:

- [[GENERATE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/generateall-function-dax](https://docs.microsoft.com/en-us/dax/generateall-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
