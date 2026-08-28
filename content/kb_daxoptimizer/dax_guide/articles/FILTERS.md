---
title: "FILTERS"
function: "filters"
category: "Table manipulation"
url: "https://dax.guide/filters/"
source: "dax.guide"
重要度:
难度:
---

# FILTERS DAX Function (Table manipulation)

Returns a table of the filter values applied directly to the specified column.

## Syntax

FILTERS ( <ColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName |  | The column for which filter values are to be returned. |

## Return values

Table A table with a single column.

A column of unique values.

## Remarks

When FILTERS is evaluated in an expression grouped in [[SUMMARIZECOLUMNS]] the original filter could be lost and replaced by the result of the [auto-exists](https://www.sqlbi.com/articles/understanding-dax-auto-exist/) behavior that combines all the filters on the same table into a single filter. The combined table resulting from this filter only contains columns explicitly listed in [[SUMMARIZECOLUMNS]] as grouping columns or filter columns.

FILTERS can have [an additional blank row](https://www.sqlbi.com/articles/blank-row-in-dax/) in case the table has at least one one-to-many relationship with other tables where there is a violation of referential integrity.

[» 1 related article](#articles)  

## Examples

```dax


--  FILTER returns the filters directly applied to a column

EVALUATE

CALCULATETABLE (

    FILTERS ( 'Product'[Category] ),

    'Product'[Category] = "Audio"

)



EVALUATE

CALCULATETABLE (

    FILTERS ( 'Product'[Category] ),

    'Product'[Category] IN { "Audio", "Computers" }

)

```

| Category |
| --- |
| Audio |

| Category |
| --- |
| Audio |
| Computers |

```dax


--  Non-existing values are not considered as filters

--  The "Bananas" category does not exist in Product table

EVALUATE

CALCULATETABLE (

    FILTERS ( 'Product'[Category] ),

    'Product'[Category] IN { "Audio", "Bananas" }

)

```

| Category |
| --- |
| Audio |

```dax


--  Filter on one column does not affect other columns,

--  which are cross-filtered but not filtered.

--  Only "Cameras and camcorders" has products with Azure color.

EVALUATE

CALCULATETABLE (

    FILTERS ( 'Product'[Category] ),

    'Product'[Color] = "Azure"

)



EVALUATE

CALCULATETABLE (

    VALUES ( 'Product'[Category] ),

    'Product'[Color] = "Azure"

)



--  Table filter include columns used in FILTERS

EVALUATE

CALCULATETABLE (

    FILTERS ( 'Product'[Category] ),

    FILTER ( 'Product', 'Product'[Color] = "Azure" )

)

```

| Category |
| --- |
| Audio |
| TV and Video |
| Computers |
| Cameras and camcorders |
| Cell phones |
| Music, Movies and Audio Books |
| Games and Toys |
| Home Appliances |

| Category |
| --- |
| Cameras and camcorders |

| Category |
| --- |
| Cameras and camcorders |

```dax


--  FILTERS returns its values even though there are no

--  rows satisfying the set of conditions.

--  The table returned by FILTERS has the correct lineage.

EVALUATE

CALCULATETABLE (

    ADDCOLUMNS (

        FILTERS ( 'Product'[Color] ),

        "#Prods", CALCULATE ( COUNTROWS ( 'Product' ) )

    ),

    'Product'[Color] IN { "White", "Azure" },

    'Product'[Brand] = "Litware"

)

```

| Color | #Prods |
| --- | --- |
| White | 45 |
| Azure | (Blank) |

## Related articles

Learn more about FILTERS in the following articles:

- [**Displaying filter context in Power BI Tooltips**](https://www.sqlbi.com/articles/displaying-filter-context-in-power-bi-tooltips/)

  This article describes how to display the filter context applied to a calculation using a special DAX measure in Power BI Tooltips. [» Read more](https://www.sqlbi.com/articles/displaying-filter-context-in-power-bi-tooltips/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/filters-function-dax](https://docs.microsoft.com/en-us/dax/filters-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
