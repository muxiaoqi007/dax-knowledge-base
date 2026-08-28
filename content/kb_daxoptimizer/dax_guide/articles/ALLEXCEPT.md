---
title: "ALLEXCEPT"
function: "allexcept"
category: "Filter"
url: "https://dax.guide/allexcept/"
source: "dax.guide"
重要度:
难度:
---

# ALLEXCEPT DAX Function (Filter)

Returns all the rows in a table except for those rows that are affected by the specified column filters.

## Syntax

ALLEXCEPT ( <TableName>, <ColumnName> [, <ColumnName> [, … ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| TableName |  | The name of an existing table. |
| ColumnName | Repeatable | A column or a table whose filtering is to be retained when ALLEXCEPT is used as a [[CALCULATE]] modifier. The column/table must be part of the expanded table specified in the first parameter. |

## Return values

Table An entire table or a table with one or more columns.

## Remarks

When used as a modifier in [[CALCULATE]] or [[CALCULATETABLE]], ALLEXCEPT removes the filters from the expanded table specified in the first argument, keeping only the filters in the columns specified in the following arguments.

When used as a table function, ALLEXCEPT materializes all the unique combinations of the columns in the table specified in the first argument that are not listed in the following arguments. In this case, the result only has the columns of the table and ignores the expanded table.  
For example, having a table T with four columns (a, b, c, d), the two following table expressions are equivalent:

```dax


    FILTER ( 

        ALLEXCEPT ( T, T[a], T[b] ),    -- The result as a table expression has only T[c] and T[d]

        <expr>

    )



    -- The result of the following expression is identical to the previous one

    FILTER ( 

        ALL ( T[c], T[d] ),             -- The result as a table expression has only T[c] and T[d]

        <expr>

    )

```

However, ALLEXCEPT is commonly used as a [[CALCULATE]] modifier and very rarely needed as a table function.

[» 6 related articles](#articles)  
[» 3 related functions](#alt)  

## Examples

Remove filters from all the columns of the Customer table but City.

```dax


CALCULATE (

    <exp>,

    ALLEXCEPT ( Customer, Customer[City] )

)

```

Remove filters from all the columns of the expanded table Sales but City.

```dax


CALCULATE (

    <exp>,

    ALLEXCEPT ( Sales, Customer[City] )

)

```

Remove filters from all the columns of the expanded table Sales but Date table and City column.

```dax


CALCULATE (

    <exp>,

    ALLEXCEPT ( Sales, 'Date', Customer[City] )

)

```

ALLEXCEPT used as a table function returns a table removing columns and duplicated rows.

```dax


--  Returns all the 'Product' columns 

EVALUATE 

ALL ( 'Product' )

    

--  Returns all the 'Product' columns but ProductKey and Product Code

EVALUATE 

ALLEXCEPT ( 'Product', 'Product'[ProductKey], 'Product'[Product Code] )

```

```dax


--  Reducing the number of columns returned 

--  also reducse the number of rows

EVALUATE

ALLEXCEPT (

    'Product',

    'Product'[ProductKey],

    'Product'[Product Code],

    'Product'[Product Name],

    'Product'[Manufacturer],

    'Product'[Brand]

)

```

```dax


-- The following query returns all the products 

-- with Contoso brand, regardless of the color

EVALUATE

CALCULATETABLE (

    CALCULATETABLE (

        'Product',

        ALLEXCEPT ( 'Product', 'Product'[Brand] )

    ),

    'Product'[Brand] = "Contoso",

    'Product'[Color] = "Red"

)

```

```dax


--  In this example, ALLEXCEPT ignores Sales expanded table filters

--  except the cross-filters coming from Date and the column Product[Color]

DEFINE 

    MEASURE Sales[# Sales] = COUNTROWS ( Sales ) 

EVALUATE

CALCULATETABLE (

    {

         ( 1, "# Sales (CY 2009 - Red)", [# Sales] ),

         ( 2, "# Sales (CY 2009)",       CALCULATE ( [# Sales], ALLEXCEPT ( Sales, 'Date' ) ) ),

         ( 3, "# Sales (Red)",           CALCULATE ( [# Sales], ALLEXCEPT ( Sales, 'Product'[Color] ) ) ),

         ( 4, "# Sales",                 CALCULATE ( [# Sales], REMOVEFILTERS ( ) ) )

    },

    'Product'[Color] = "Red",

    'Date'[Calendar Year] = "CY 2009"

)

ORDER BY [Value1]

```

| Value1 | Value2 | Value3 |
| --- | --- | --- |
| 1 | # Sales (CY 2009 – Red) | 2,038 |
| 2 | # Sales (CY 2009) | 39,793 |
| 3 | # Sales (Red) | 5,802 |
| 4 | # Sales | 100,231 |

```dax


-- ALLEXCEPT has a different behavior if used as a table function 

-- or as a CALCULATE modifier.

EVALUATE

CALCULATETABLE (

    {

         ( "CALCULATE FILTER", 

             CALCULATE ( 

                 COUNTROWS ( Sales ), 

                 ALLEXCEPT ( Sales, 'Date' ) ) 

             ),

         ( "TABLE FUNCTION", 

             COUNTROWS ( 

                 ALLEXCEPT ( Sales, 'Date' ) ) 

             )

    },

    'Date'[Calendar Year] = "CY 2009",

    'Product'[Color] = "Red"

)

```

| Value1 | Value2 |
| --- | --- |
| CALCULATE FILTER | 39,793 |
| TABLE FUNCTION | 100,231 |

## Related articles

Learn more about ALLEXCEPT in the following articles:

- [**Managing “all” functions in DAX: ALL, ALLSELECTED, ALLNOBLANKROW, ALLEXCEPT**](https://www.sqlbi.com/articles/managing-all-functions-in-dax-all-allselected-allnoblankrow-allexcept/)

  This article provides a complete explanation of the behavior of the ALLxxx functions in DAX. When used as filters in CALCULATE, ALLxxx functions might display unexpected behaviors. [» Read more](https://www.sqlbi.com/articles/managing-all-functions-in-dax-all-allselected-allnoblankrow-allexcept/)
- [**Using ALLEXCEPT versus ALL and VALUES**](https://www.sqlbi.com/articles/using-allexcept-versus-all-and-values/)

  This article describes the semantic difference between ALLEXCEPT and the joint use of ALL and VALUES, showing practical examples of the different results in Power BI and SSAS 2016. [» Read more](https://www.sqlbi.com/articles/using-allexcept-versus-all-and-values/)
- [**Expanded tables in DAX**](https://www.sqlbi.com/articles/expanded-tables-in-dax/)

  Expanded tables are the core of DAX; understanding how they work is of paramount importance. This article provides a theoretical foundation of what expanded tables are, along with fundamental concepts useful when reading DAX code. [» Read more](https://www.sqlbi.com/articles/expanded-tables-in-dax/)
- [**Understanding Circular Dependencies in Tabular and PowerPivot**](https://www.sqlbi.com/articles/understanding-circular-dependencies/)

  When you design a data model for Tabular you should pay attention to a though topic, which is that of circular dependencies in formulas. It is very important to learn how to handle circular dependencies now because in SQL 2012… [» Read more](https://www.sqlbi.com/articles/understanding-circular-dependencies/)
- [**Comparing different school terms in Power BI**](https://www.sqlbi.com/articles/comparing-different-school-terms-in-power-bi/)

  This article describes how to implement the comparison between school terms. The same technique can be applied to any arbitrary time periods that do not match regular months or quarters in a calendar, such as seasons or campaigns. [» Read more](https://www.sqlbi.com/articles/comparing-different-school-terms-in-power-bi/)
- [**ALL vs ALLSELECTED vs ALLEXCEPT vs REMOVEFILTERS**](https://www.sqlbi.com/articles/all-vs-allselected-vs-allexcept-vs-removefilters/)

  DAX offers many functions to remove filters from the filter context. In this article, we analyze the differences among the most commonly-used functions. [» Read more](https://www.sqlbi.com/articles/all-vs-allselected-vs-allexcept-vs-removefilters/)

## Related functions

Other related functions are:

- [[ALL]]
- [[ALLNOBLANKROW]]
- [[ALLSELECTED]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Albe Gouws, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/allexcept-function-dax](https://docs.microsoft.com/en-us/dax/allexcept-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
