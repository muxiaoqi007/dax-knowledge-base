---
title: "ADDMISSINGITEMS"
function: "addmissingitems"
category: "Table manipulation"
url: "https://dax.guide/addmissingitems/"
source: "dax.guide"
重要度:
难度:
---

# ADDMISSINGITEMS DAX Function (Table manipulation)

Add the rows with empty measure values back.

## Syntax

ADDMISSINGITEMS ( [<ShowAll\_ColumnName> [, <ShowAll\_ColumnName> [, … ] ] ], <Table> [, <GroupBy\_ColumnName> [, [<FilterTable>] [, <GroupBy\_ColumnName> [, [<FilterTable>] [, … ] ] ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ShowAll\_ColumnName | Optional Repeatable | ShowAll columns. |
| Table |  | A SummarizeColumns table. |
| GroupBy\_ColumnName | Optional Repeatable | A column to group by or a call to [[ROLLUPGROUP]] function and [[ROLLUPADDISSUBTOTAL]] function to specify a list of columns to group by with subtotals. |
| FilterTable | Optional Repeatable | An expression that defines the table from which rows are to be returned. |

## Return values

Table An entire table or a table with one or more columns.

[» 1 related function](#alt)  

## Examples

```dax


--  ADDMISSINGITEMS adds rows that would be skipped by SUMMARIZECOLUMNS

--  when the value of all the measures is blank.

--  In the example, years 2005, 2006 and 2010 and 2011 are added back by

--  ADDMISSINGITEMS in the second result.

--  SUMMARIZECOLUMNS in the first query does not return them, because there

--  were no sales.

EVALUATE 

SUMMARIZECOLUMNS (

    'Date'[Calendar Year],

    "Amt", [Sales Amount]

)



EVALUATE

ADDMISSINGITEMS (

    'Date'[Calendar Year],

    SUMMARIZECOLUMNS (

        'Date'[Calendar Year],

        "Amt", [Sales Amount]

    ),

    'Date'[Calendar Year]

)

ORDER BY [Calendar Year] ASC

```

| Calendar Year | Amt |
| --- | --- |
| 2007-01-01 | 11,309,946.12 |
| 2008-01-01 | 9,927,582.99 |
| 2009-01-01 | 9,353,814.87 |

| Calendar Year | Amt |
| --- | --- |
| 2005-01-01 | (Blank) |
| 2006-01-01 | (Blank) |
| 2007-01-01 | 11,309,946.12 |
| 2008-01-01 | 9,927,582.99 |
| 2009-01-01 | 9,353,814.87 |
| 2010-01-01 | (Blank) |
| 2011-01-01 | (Blank) |

```dax


--  With ADDMISSINGITEMS you specify the columns to add (first set)

--  and the column used to group by (second set)

--  In the example, even though the grouping happens by year and month

--  ADDMISSINGITEMS adds back only the year rows

EVALUATE

ADDMISSINGITEMS (

    'Date'[Calendar Year],

    SUMMARIZECOLUMNS (

        'Date'[Calendar Year],

        'Date'[Calendar Year Month],

        "Amt", [Sales Amount]

    ),

    'Date'[Calendar Year]

)

```

```dax


--  You can also specify table filters applied to the filter context

--  while retrieving the missing items.

--  In this example, ADDMISSINGITEMS only returns 2010 and 2011, 

--  because of the last filter that removes 2005 and 2006

EVALUATE

ADDMISSINGITEMS (

    'Date'[Calendar Year],

    SUMMARIZECOLUMNS (

        'Date'[Calendar Year],

        "Amt", [Sales Amount]

    ),

    'Date'[Calendar Year],

    EXCEPT ( ALL ( 'Date'[Calendar Year] ), { "CY 2005", "CY 2006" } )

)

```

| Calendar Year | Amt |
| --- | --- |
| CY 2007 | 11,309,946.12 |
| CY 2008 | 9,927,582.99 |
| CY 2009 | 9,353,814.87 |
| CY 2010 | (Blank) |
| CY 2011 | (Blank) |

## Related functions

Other related functions are:

- [[SUMMARIZECOLUMNS]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Jocelyn Saw, Pedro Gravanita, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/addmissingitems-function-dax](https://docs.microsoft.com/en-us/dax/addmissingitems-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
