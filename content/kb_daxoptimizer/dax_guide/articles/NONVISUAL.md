---
title: "NONVISUAL"
function: "nonvisual"
category: "Table manipulation"
url: "https://dax.guide/nonvisual/"
source: "dax.guide"
重要度:
难度:
---

# NONVISUAL DAX Function (Table manipulation)

Mark the filter as NonVisual.

## Syntax

NONVISUAL ( <Expression> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Expression |  | Filter expression. |

## Return values

Table An entire table or a table with one or more columns.

A table of values.

## Remarks

Marks a value filter in [[SUMMARIZECOLUMNS]] function as not affecting measure values, but only applying to group-by columns.

[» 2 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  NONVISUAL marks a SUMMARIZECOLUMNS filter so that it does

--  not affect measures using ALLSELECTED as part of their

--  calculation.

--  It can be used to obtain non-visual totals at the query level.

DEFINE MEASURE Sales[Amount ALLSELECTED] =

    CALCULATE ( [Sales Amount], ALLSELECTED ( 'Date'[Calendar Year] ) )

    

EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Calendar Year],

    TREATAS ( { "CY 2008", "CY 2009" }, 'Date'[Calendar Year] ),

    "Amount", [Sales Amount],

    "Amount ALLSELECTED", [Amount ALLSELECTED]

)



EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Calendar Year],

    NONVISUAL ( TREATAS ( { "CY 2008", "CY 2009" }, 'Date'[Calendar Year] ) ),

    "Amount", [Sales Amount],

    "Amount ALLSELECTED", [Amount ALLSELECTED]

)

```

| Calendar Year | Amount | Amount ALLSELECTED |
| --- | --- | --- |
| 2008-01-01 | 9,927,582.99 | 19,281,397.86 |
| 2009-01-01 | 9,353,814.87 | 19,281,397.86 |

| Calendar Year | Amount | Amount ALLSELECTED |
| --- | --- | --- |
| 2008-01-01 | 9,927,582.99 | 30,591,343.98 |
| 2009-01-01 | 9,353,814.87 | 30,591,343.98 |

## Related articles

Learn more about NONVISUAL in the following articles:

- [**SQLBI+ updates in August 2025**](https://www.sqlbi.com/blog/marco/2025/08/05/sqlbi-updates-in-august-2025/)

  For many years, SUMMARIZECOLUMNS has been a function dedicated to DAX queries and calculated tables, but it was not supported in DAX measures. Over time, Microsoft lifted the limitations, and in June 2024, the function was declared as fully supported in measures. However, we never suggested widely adopting it because we wanted to study its behavior in detail, which was not necessary for the queries due to the limited side effects in that context. Time well spent, as we have now been able to document in detail how SUMMARIZECOLUMNS works, what to do, and what not to do. You will… [» Read more](https://www.sqlbi.com/blog/marco/2025/08/05/sqlbi-updates-in-august-2025/)
- [**SUMMARIZECOLUMNS best practices**](https://www.sqlbi.com/articles/summarizecolumns-best-practices/)

  SUMMARIZECOLUMNS is a powerful and complex function in DAX that in 2025 can be used in measures. This article outlines the best practices when using this function to avoid incorrect results. [» Read more](https://www.sqlbi.com/articles/summarizecolumns-best-practices/)

## Related functions

Other related functions are:

- [[SUMMARIZECOLUMNS]]
- [[ALLSELECTED]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/nonvisual-function-dax](https://docs.microsoft.com/en-us/dax/nonvisual-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
