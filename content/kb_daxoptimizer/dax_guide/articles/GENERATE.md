---
title: "GENERATE"
function: "generate"
category: "Table manipulation"
url: "https://dax.guide/generate/"
source: "dax.guide"
重要度:
难度:
---

# GENERATE DAX Function (Table manipulation)

The second table expression will be evaluated for each row in the first table. Returns the crossjoin of the first table with these results.

## Syntax

GENERATE ( <Table1>, <Table2> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table1  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | The base table in Generate. || Table2  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | A table expression that will be evaluated for each row in the first table. |

## Return values

Table An entire table or a table with one or more columns.

## Remarks

- If the evaluation of *table2* for the current row in *table1* returns an empty table, then the current row from *table1* will **not** be included in the results. This is different than [[GENERATEALL]]() where the current row from *table1* will be included in the results and columns corresponding to *table2* will have null values for that row.
- All column names from *table1* and *table2* must be different or an error is returned.

[» 7 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  GENERATE is an iterator: the second argument is evaluated in a row context

DEFINE

VAR Dates = 

    UNION ( 

        ROW ( "FirstDate", DATE ( 2007, 1, 1 ), "LastDate", DATE ( 2007, 1, 3 ) ),

        ROW ( "FirstDate", DATE ( 2007, 1, 9 ), "LastDate", DATE ( 2007, 1, 12 ) )

    )

VAR DatesExpanded = 

    GENERATE ( 

        Dates,

        DATESBETWEEN ( 'Date'[Date], [FirstDate], [LastDate] )

    )



EVALUATE Dates



EVALUATE DatesExpanded

```

| FirstDate | LastDate |
| --- | --- |
| 2007-01-01 | 2007-01-03 |
| 2007-01-09 | 2007-01-12 |

| FirstDate | LastDate | Date |
| --- | --- | --- |
| 2007-01-01 | 2007-01-03 | 2007-01-01 |
| 2007-01-01 | 2007-01-03 | 2007-01-02 |
| 2007-01-01 | 2007-01-03 | 2007-01-03 |
| 2007-01-09 | 2007-01-12 | 2007-01-09 |
| 2007-01-09 | 2007-01-12 | 2007-01-10 |
| 2007-01-09 | 2007-01-12 | 2007-01-11 |
| 2007-01-09 | 2007-01-12 | 2007-01-12 |

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

Learn more about GENERATE in the following articles:

- [**Using GENERATE and ROW instead of ADDCOLUMNS in DAX**](https://www.sqlbi.com/articles/using-generate-and-row-instead-of-addcolumns-in-dax/)

  This article explains how to improve DAX queries using GENERATE and ROW instead of ADDCOLUMNS when you create table expressions. [» Read more](https://www.sqlbi.com/articles/using-generate-and-row-instead-of-addcolumns-in-dax/)
- [**Transition Matrix Using Calculated Tables**](https://www.sqlbi.com/articles/transition-matrix-using-calculated-tables/)

  In the 2015 September update, Power BI introduced calculated tables, which are computed using DAX expressions instead of being loaded from a data source. This article shows the usage of calculated tables to solve the pattern of transition matrix for… [» Read more](https://www.sqlbi.com/articles/transition-matrix-using-calculated-tables/)
- [**Lookup multiple values in DAX**](https://www.sqlbi.com/articles/lookup-multiple-values-in-dax/)

  This article describes different techniques to retrieve multiple values from a lookup table in DAX, improving code readability and performance. [» Read more](https://www.sqlbi.com/articles/lookup-multiple-values-in-dax/)
- [**Filtering the Top 3 products for each category in Power BI**](https://www.sqlbi.com/articles/filtering-the-top-3-products-for-each-category-in-power-bi/)

  This article describes different techniques to display the first three products for each category in Power BI. It includes considerations on how to adapt the technique to different models and requirements. [» Read more](https://www.sqlbi.com/articles/filtering-the-top-3-products-for-each-category-in-power-bi/)
- [**Using join functions in DAX**](https://www.sqlbi.com/articles/using-join-functions-in-dax/)

  This article describes the practical uses of NATURALLEFTOUTERJOIN and NATURALINNERJOIN in DAX. These functions are not commonly used in DAX because they do not have the same flexibility as the corresponding concepts in SQL. [» Read more](https://www.sqlbi.com/articles/using-join-functions-in-dax/)
- [**Preparing a data model for Sankey Charts in Power BI**](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)

  This article describes how to correctly shape a data model and prepare data to use a Sankey Chart as a funnel, considering events related to a customer (contact, trial, subscription, renewal, and others). [» Read more](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)
- [**Account receivable aging in Power BI**](https://www.sqlbi.com/articles/account-receivable-aging-in-power-bi/)

  This article describes an Accounts Receivable Aging report in Power BI, and shows how to simplify a business problem using existing modeling patterns. [» Read more](https://www.sqlbi.com/articles/account-receivable-aging-in-power-bi/)

## Related functions

Other related functions are:

- [[GENERATEALL]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/generate-function-dax](https://docs.microsoft.com/en-us/dax/generate-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
