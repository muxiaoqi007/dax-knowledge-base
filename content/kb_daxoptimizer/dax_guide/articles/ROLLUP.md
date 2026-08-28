---
title: "ROLLUP"
function: "rollup"
category: "Table manipulation"
url: "https://dax.guide/rollup/"
source: "dax.guide"
重要度:
难度:
---

# ROLLUP DAX Function (Table manipulation)

Identifies a subset of columns specified in the call to [[SUMMARIZE]] function that should be used to calculate subtotals.

## Syntax

ROLLUP ( <GroupBy\_ColumnName> [, <GroupBy\_ColumnName> [, … ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| GroupBy\_ColumnName | Repeatable | The GroupBy\_ColumnName parameter must be a qualified name of an existing column to be used to create summary groups based on the values found in it.  The parameter cannot be an expression. |

## Return values

The function does not return a value. It only specifies the set of columns to be subtotaled.

## Remarks

The ROLLUP function is used exclusively within [[SUMMARIZE]].

The columns mentioned in the ROLLUP expression cannot be referenced as part of a GroupBy\_columnName columns in [[SUMMARIZE]].

[» 2 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  ROLLUP in SUMMARIZE computes subtotals for the specified columns.

--  Subtotal rows can be detected through ISSUBTOTAL

EVALUATE

CALCULATETABLE (

    SUMMARIZE (

        Sales,

        ROLLUP ( 'Date'[Calendar Year] ),

        "Year total", ISSUBTOTAL ( 'Date'[Calendar Year] ),

        "Amount", [Sales Amount]

    ),

    TREATAS ( { "CY 2008", "CY 2009" }, 'Date'[Calendar Year] ),

    TREATAS ( { "Bachelors", "Partial College" }, Customer[Education] )

)

ORDER BY

    [Year total],

    [Calendar Year]

```

| Calendar Year | Year total | Amount |
| --- | --- | --- |
| 2008-01-01 | false | 747,365.53 |
| 2009-01-01 | false | 362,354.56 |
| (Blank) | true | 1,109,720.09 |

```dax


--  You can specify multiple columns in the same ROLLUP 

--  to produce multiple levels of subtotals.

--  SUMMARIZE does not support multiple ROLLUP, whereas

--  SUMMARIZECOLUMNS does support multiple rollup conditions by 

--  using ROLLUPADDISSUBTOTAL

EVALUATE

CALCULATETABLE (

    SUMMARIZE (

        Sales,

        ROLLUP ( 'Date'[Calendar Year], Customer[Education] ),

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
| 2008-01-01 | (Blank) | false | true | 747,365.53 |
| 2009-01-01 | Bachelors | false | false | 189,037.54 |
| 2009-01-01 | Partial College | false | false | 173,317.03 |
| 2009-01-01 | (Blank) | false | true | 362,354.56 |
| (Blank) | (Blank) | true | true | 1,109,720.09 |

```dax


--  Using ROLLUPGROUP you can reduce the number of subtotals

--  by grouping together several columns.

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

## Related articles

Learn more about ROLLUP in the following articles:

- [**Best Practices Using SUMMARIZE and ADDCOLUMNS**](https://www.sqlbi.com/articles/best-practices-using-summarize-and-addcolumns/)

  Everyone using DAX is probably used to SQL query language. Because of the similarities between the Tabular data modeling and the relational data modeling, there is the expectation that you can perform the same operations as those allowed in SQL. However, in its current implementation DAX does not permit all the operations that you can […] [» Read more](https://www.sqlbi.com/articles/best-practices-using-summarize-and-addcolumns/)
- [**All the secrets of SUMMARIZE**](https://www.sqlbi.com/articles/all-the-secrets-of-summarize/)

  SUMMARIZE is a function that looks quite simple, but its functionality hides some secrets that might surprise even seasoned DAX coders. In this article, we analyze the behavior of SUMMARIZE, in order to completely describe its semantic. The final advice might surprise you: we will suggest to avoid the use of SUMMARIZE in your code, […] [» Read more](https://www.sqlbi.com/articles/all-the-secrets-of-summarize/)

## Related functions

Other related functions are:

- [[SUMMARIZE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/rollup-function-dax](https://docs.microsoft.com/en-us/dax/rollup-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
