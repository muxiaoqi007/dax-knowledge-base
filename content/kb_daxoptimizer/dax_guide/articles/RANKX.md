---
title: "RANKX"
function: "rankx"
category: "Statistical"
url: "https://dax.guide/rankx/"
source: "dax.guide"
重要度:
难度:
---

# RANKX DAX Function (Statistical)

Returns the rank of an expression evaluated in the current context in the list of values for the expression evaluated for each row in the specified table.

## Syntax

RANKX ( <Table>, <Expression> [, <Value>] [, <Order>] [, <Ties>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | A table expression. || Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | An expression that will be evaluated for row of the table. |
| Value | Optional | An expression that will be evaluated in the current context. If omitted, the Expression argument will be used. |
| Order | Optional | The order to be applied. 0/FALSE/DESC – descending; 1/TRUE/ASC – ascending. |
| Ties | Optional | Function behavior in the event of ties. Skip – ranks that correspond to elements in ties will be skipped; Dense – all elements in a tie are counted as one. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

The rank number of Value among all possible values of Expression evaluated for all rows of Table numbers.

## Remarks

The default value for the Order argument is DESC.  
The default value for the Ties argument is SKIP.  
The Expression argument is evaluated in a row context: while a measure reference implies a context transition, an explicit [[CALCULATE]] might be necessary using aggregation functions such as [[SUM]].

**You should consider using [[RANK]] instead of RANKX:** When one or both the Expression or the Value arguments have a [Decimal](https://dax.guide/dt/decimal/) data type (which is Decimal Number in Power BI), RANKX may return unexpected results because of different approximation in storing the underlying floating point number. To avoid unexpected results, use [[RANK]], or change the data type to [Currency](https://dax.guide/dt/currency/) data type (which is Fixed Decimal Number in Power BI), or force rounding by using [[ROUND]].

[» 8 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  RANKX computes the ranking of an expression over a table

--  The expression is evaluated during the iteration over the

--  table and then in the evaluation context of RANKX.

--  The result is the position of the outer evaluation in the 

--  lookup table built during the iteration

DEFINE

    VAR BrandsAndSales =

        ADDCOLUMNS (

            VALUES ( 'Product'[Brand] ),

            "@Amt", [Sales Amount]

        )

EVALUATE

ADDCOLUMNS (

    BrandsAndSales,

    "Rank",

        RANKX (

            BrandsAndSales,

            [@Amt]

        )

)

ORDER BY [@Amt] DESC

```

| Brand | @Amt | Rank |
| --- | --- | --- |
| Contoso | 7,352,399.03 | 1 |
| Fabrikam | 5,554,015.73 | 2 |
| Adventure Works | 4,011,112.28 | 3 |
| Litware | 3,255,704.03 | 4 |
| Proseware | 2,546,144.16 | 5 |
| A. Datum | 2,096,184.64 | 6 |
| Wide World Importers | 1,901,956.66 | 7 |
| Southridge Video | 1,384,413.85 | 8 |
| The Phone Company | 1,123,819.07 | 9 |
| Northwind Traders | 1,040,552.13 | 10 |
| Tailspin Toys | 325,042.42 | 11 |

```dax


--  The third argument of RANKX is useful when the outer 

--  evaluation requires a different expression than the

--  inner one. For example, when ranking an expression over

--  a pre-built lookup table

DEFINE

    VAR SalesLevels =

        SELECTCOLUMNS ( { 6000000, 3000000, 1500000, 750000, 0 }, "@Limit", [Value] )



EVALUATE

ADDCOLUMNS (

    VALUES ( Product[Brand] ),

    "Sales Amount", [Sales Amount],

    "Level", RANKX ( SalesLevels, [@Limit], [Sales Amount] )

)

ORDER BY [Level] ASC

```

| Brand | Sales Amount | Level |
| --- | --- | --- |
| Contoso | 7,352,399.03 | 1 |
| Litware | 3,255,704.03 | 2 |
| Adventure Works | 4,011,112.28 | 2 |
| Fabrikam | 5,554,015.73 | 2 |
| Wide World Importers | 1,901,956.66 | 3 |
| Proseware | 2,546,144.16 | 3 |
| A. Datum | 2,096,184.64 | 3 |
| Southridge Video | 1,384,413.85 | 4 |
| Northwind Traders | 1,040,552.13 | 4 |
| The Phone Company | 1,123,819.07 | 4 |
| Tailspin Toys | 325,042.42 | 5 |

```dax


--  The fourth argument of RANKX specifies the order of ranking

--  it can be DESC (default) or ASC

DEFINE

    VAR BrandsAndSales =

        ADDCOLUMNS ( VALUES ( 'Product'[Brand] ), "@Amt", [Sales Amount] )

EVALUATE

ADDCOLUMNS (

    BrandsAndSales,

    "Rank ASC",  RANKX ( BrandsAndSales, [@Amt], [@Amt], ASC ),

    "Rank DESC", RANKX ( BrandsAndSales, [@Amt], [@Amt], DESC ),

    "Rank (default)", RANKX ( BrandsAndSales, [@Amt], [@Amt] )

)

ORDER BY [@Amt] DESC

```

| Brand | @Amt | Rank ASC | Rank DESC | Rank (default) |
| --- | --- | --- | --- | --- |
| Contoso | 7,352,399.03 | 11 | 1 | 1 |
| Fabrikam | 5,554,015.73 | 10 | 2 | 2 |
| Adventure Works | 4,011,112.28 | 9 | 3 | 3 |
| Litware | 3,255,704.03 | 8 | 4 | 4 |
| Proseware | 2,546,144.16 | 7 | 5 | 5 |
| A. Datum | 2,096,184.64 | 6 | 6 | 6 |
| Wide World Importers | 1,901,956.66 | 5 | 7 | 7 |
| Southridge Video | 1,384,413.85 | 4 | 8 | 8 |
| The Phone Company | 1,123,819.07 | 3 | 9 | 9 |
| Northwind Traders | 1,040,552.13 | 2 | 10 | 10 |
| Tailspin Toys | 325,042.42 | 1 | 11 | 11 |

```dax


--  The fifth argument of RANKX specifies the behavior in

--  case of ties. SKIP (default) skips positions, whereas

--  DENSE guarantees a 1-step increment in the ranking

DEFINE

    VAR BrandsAndSales =

        ADDCOLUMNS (

            VALUES ( 'Product'[Brand] ),

            "@Amt", MROUND ( [Sales Amount], 1E6 )

        )

EVALUATE

ADDCOLUMNS (

    BrandsAndSales,

    "Rank SKIP",  RANKX ( BrandsAndSales, [@Amt], [@Amt], DESC, SKIP ),

    "Rank DENSE", RANKX ( BrandsAndSales, [@Amt], [@Amt], DESC, DENSE )

)

ORDER BY [@Amt] DESC

```

| Brand | @Amt | Rank SKIP | Rank DENSE |
| --- | --- | --- | --- |
| Contoso | 7,000,000 | 1 | 1 |
| Fabrikam | 6,000,000 | 2 | 2 |
| Adventure Works | 4,000,000 | 3 | 3 |
| Litware | 3,000,000 | 4 | 4 |
| Proseware | 3,000,000 | 4 | 4 |
| Wide World Importers | 2,000,000 | 6 | 5 |
| A. Datum | 2,000,000 | 6 | 5 |
| Northwind Traders | 1,000,000 | 8 | 6 |
| The Phone Company | 1,000,000 | 8 | 6 |
| Southridge Video | 1,000,000 | 8 | 6 |
| Tailspin Toys | 0 | 11 | 7 |

## Related articles

Learn more about RANKX in the following articles:

- [**Use of RANKX in Power BI measures**](https://www.sqlbi.com/articles/use-of-rankx-in-power-bi-measures/)

  The RANKX function in Power BI might have an unexpected behavior when applied to a column that has a specific sort order in the data model. This article explains why, and how to address this issue. [» Read more](https://www.sqlbi.com/articles/use-of-rankx-in-power-bi-measures/)
- [**Handling customers with the same name in Power BI**](https://www.sqlbi.com/articles/handling-customers-with-the-same-name-in-power-bi/)

  This article explains how to show different customers with the same name in a Power BI report by using zero-width spaces, thus simplifying the presentation without adding visible characters to make the names unique. [» Read more](https://www.sqlbi.com/articles/handling-customers-with-the-same-name-in-power-bi/)
- [**Sorting duplicated names in a level of a hierarchy with DAX**](https://www.sqlbi.com/articles/sorting-duplicated-names-in-a-level-of-a-hierarchy-with-dax/)

  This article describes how to use DAX calculated columns to sort names that look like duplicates at a certain level of a hierarchy, but are unique when considering their full path within the hierarchy. [» Read more](https://www.sqlbi.com/articles/sorting-duplicated-names-in-a-level-of-a-hierarchy-with-dax/)
- [**How to compute index numbers at top speed**](https://www.sqlbi.com/articles/how-to-compute-index-numbers-at-top-speed/)

  This article presents different techniques to compute a rownumber column in DAX based on a specific ranking, comparing slow and optimized approaches. [» Read more](https://www.sqlbi.com/articles/how-to-compute-index-numbers-at-top-speed/)
- [**Displaying Nth Element in DAX**](https://www.sqlbi.com/articles/displaying-nth-element-in-dax/)

  This article describes how to create a measure displaying the name or value of an element that has a specific ranking, with different option for managing ties. [» Read more](https://www.sqlbi.com/articles/displaying-nth-element-in-dax/)
- [**Introducing RANKX in DAX**](https://www.sqlbi.com/articles/introducing-rankx-in-dax/)

  RANKX is a simple function used to rank a value within a list of values. Its use is simple, but it can be a source of frustration for newbies. In this article we introduce the RANKX function with a few examples. [» Read more](https://www.sqlbi.com/articles/introducing-rankx-in-dax/)
- [**RANKX on multiple columns with DAX and Power BI**](https://www.sqlbi.com/articles/rankx-on-multiple-columns-with-dax-and-power-bi/)

  This article shows techniques to obtain a ranking based on more than one column. The ranking can be both static and dynamic. [» Read more](https://www.sqlbi.com/articles/rankx-on-multiple-columns-with-dax-and-power-bi/)
- [**Using RANK instead of RANKX in DAX**](https://www.sqlbi.com/articles/using-rank-instead-of-rankx-in-dax/)

  Should you use RANK or stick with RANKX? In which scenarios is one better than the other? This article provides an in-depth analysis to help readers make informed choices. [» Read more](https://www.sqlbi.com/articles/using-rank-instead-of-rankx-in-dax/)

## Related functions

Other related functions are:

- [[RANK.EQ]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Vinícius Nader

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/rankx-function-dax](https://docs.microsoft.com/en-us/dax/rankx-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
