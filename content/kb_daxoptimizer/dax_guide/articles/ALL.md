---
title: "ALL"
function: "all"
category: "Filter"
url: "https://dax.guide/all/"
source: "dax.guide"
重要度: "5"
难度: "2"
---

# ALL DAX Function (Filter)

Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied.

## Syntax

ALL ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| TableNameOrColumnName | Optional | The name of an existing table or column. |
| ColumnName | Optional Repeatable | A column in the same base table. The column can be specified in optional parameters only when a column is used in the first argument, too. |

## Return values

Table An entire table or a table with one or more columns.

## Remarks

This function removes the corresponding filters from the filter context, just as [[REMOVEFILTERS]] does. It does not materialize the resulting table when called directly in a filter argument of [[CALCULATE]] or [[CALCULATETABLE]].

ALL can be used as a table expression when it has at least one argument.  
ALL without arguments can be used only as a [[CALCULATE]] or [[CALCULATETABLE]] modifier and removes all the filters from the filter context.

The following remarks are valid using ALL as a table expression:

- Using a table argument, ALL returns all the rows of the table including any duplicated rows.
- Using a single column argument, ALL returns all the unique values of the column.
- Using two or more columns arguments, ALL returns all the unique combinations of values in multiple columns.
- In every case, ALL includes in the result the additional blank row [generated for invalid relationships](https://www.sqlbi.com/articles/blank-row-in-dax/).

[» 6 related articles](#articles)  
[» 4 related functions](#alt)  

## Examples

The ALL function can be applied to either a table or a set of columns.

```dax


ALL ( Customer )



ALL ( Customer[Country], Customer[State] , Customer[City] )

```

```dax


--

--  This query returns all the products, 

--  ignoring the filter on product color

--

EVALUATE

CALCULATETABLE (

    ALL ( 'Product' ),

    'Product'[Color] = "Red"

)

```

```dax


EVALUATE 

CALCULATETABLE (

    ALL ( 'Product'[Color] ),

    'Product'[Color] = "Red"

)

ORDER BY 'Product'[Color]

```

| Color |
| --- |
| Azure |
| Black |
| Blue |
| Brown |
| Gold |
| Green |
| Grey |
| Orange |
| Pink |
| Purple |
| Red |
| Silver |
| Silver Grey |
| Transparent |
| White |
| Yellow |

```dax


EVALUATE

CALCULATETABLE (

    ALL (

        'Product'[Brand],

        'Product'[Color]

    ),

    'Product'[Color] = "Red"

)

ORDER BY

    'Product'[Brand],

    'Product'[Color]



```

| Brand | Color |
| --- | --- |
| A. Datum | Azure |
| A. Datum | Black |
| … | … |
| Contoso | Black |
| Contoso | Blue |
| … | … |
| The Phone Company | Black |
| The Phone Company | Gold |
| … | … |
| Wide World Importers | Black |
| Wide World Importers | Gold |
| … | … |
| Wide World Importers | White |
| Wide World Importers | Yellow |

```dax


--

--  This query returns all the products,

--  ignoring the filter on product color

--  ALL as a filter modifier is like REMOVEFILTERS

--

EVALUATE

CALCULATETABLE (

    CALCULATETABLE (

        'Product',

        ALL ( 'Product'[Color] )   -- same as REMOVEFILTERS ( 'Product'[Color] )

    ),

    'Product'[Color] = "Red"

)

ORDER BY

    'Product'[Brand],

    'Product'[Color]

```

```dax


--

--  This query returns all the products,

--  removing any filter from the filter context.

--  ALL as a filter modifier is like REMOVEFILTERS.

--

EVALUATE

CALCULATETABLE (

    CALCULATETABLE (

        'Product',

        ALL ( )   -- same as REMOVEFILTERS (  )

    ),

    'Product'[Color] = "Red"

)

ORDER BY

    'Product'[Brand],

    'Product'[Color]

```

```dax


--

--  ALL with a table works on the expanded table, removing filters

--  from any column in the expanded table

--

EVALUATE

CALCULATETABLE (

    {

         ( "Sales Amount ", [Sales Amount] ),

         ( "Sales Amount (ALL Colors)", CALCULATE (

                                            [Sales Amount],

                                            ALL ( 'Product'[Color] )

                                        ) 

         ),

         ( "Sales Amount (ALL Products)", CALCULATE (

                                              [Sales Amount],

                                              ALL ( 'Product' )

                                          ) 

         ),

         ( "Sales Amount (ALL)", CALCULATE (

                                     [Sales Amount],

                                     ALL ()

                                 ) 

         ),

         ( "Sales Amount (ALL Sales)", CALCULATE (

                                           [Sales Amount],

                                           ALL ( Sales )

                                       )

         )

    },

    'Product'[Color] = "Red",

    'Date'[Calendar Year] = "CY 2008"

)



```

| Value1 | Value2 |
| --- | --- |
| Sales Amount | 395,277.22 |
| Sales Amount (ALL Colors) | 9,927,582.99 |
| Sales Amount (ALL Products) | 9,927,582.99 |
| Sales Amount (ALL) | 30,591,343.98 |
| Sales Amount (ALL Sales) | 30,591,343.98 |

## Related articles

Learn more about ALL in the following articles:

- [**Managing “all” functions in DAX: ALL, ALLSELECTED, ALLNOBLANKROW, ALLEXCEPT**](https://www.sqlbi.com/articles/managing-all-functions-in-dax-all-allselected-allnoblankrow-allexcept/)

  This article provides a complete explanation of the behavior of the ALLxxx functions in DAX. When used as filters in CALCULATE, ALLxxx functions might display unexpected behaviors. [» Read more](https://www.sqlbi.com/articles/managing-all-functions-in-dax-all-allselected-allnoblankrow-allexcept/)
- [**Using ALLEXCEPT versus ALL and VALUES**](https://www.sqlbi.com/articles/using-allexcept-versus-all-and-values/)

  This article describes the semantic difference between ALLEXCEPT and the joint use of ALL and VALUES, showing practical examples of the different results in Power BI and SSAS 2016. [» Read more](https://www.sqlbi.com/articles/using-allexcept-versus-all-and-values/)
- [**Context Transition and Expanded Tables**](https://www.sqlbi.com/articles/context-transition-and-expanded-tables/)

  This article describes how table expansion and filter context propagation are important DAX concepts to understand and fix small glitches in DAX expressions. [» Read more](https://www.sqlbi.com/articles/context-transition-and-expanded-tables/)
- [**Blank row in DAX**](https://www.sqlbi.com/articles/blank-row-in-dax/)

  There are two functions in DAX that return the list of values of a column: VALUES and DISTINCT. This article describes the difference between the two, explaining the details of the blank row added to tables for invalid relationships. [» Read more](https://www.sqlbi.com/articles/blank-row-in-dax/)
- [**Differences between ALL and ALLCROSSFILTERED**](https://www.sqlbi.com/articles/differences-between-all-and-allcrossfiltered/)

  This article describes the differences between the ALL and CROSSFILTERED functions in DAX. [» Read more](https://www.sqlbi.com/articles/differences-between-all-and-allcrossfiltered/)
- [**ALL vs ALLSELECTED vs ALLEXCEPT vs REMOVEFILTERS**](https://www.sqlbi.com/articles/all-vs-allselected-vs-allexcept-vs-removefilters/)

  DAX offers many functions to remove filters from the filter context. In this article, we analyze the differences among the most commonly-used functions. [» Read more](https://www.sqlbi.com/articles/all-vs-allselected-vs-allexcept-vs-removefilters/)

## Related functions

Other related functions are:

- [[ALLEXCEPT]]
- [[ALLNOBLANKROW]]
- [[ALLSELECTED]]
- [[REMOVEFILTERS]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/all-function-dax](https://docs.microsoft.com/en-us/dax/all-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
