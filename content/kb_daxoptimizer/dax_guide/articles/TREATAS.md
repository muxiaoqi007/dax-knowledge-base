---
title: "TREATAS"
function: "treatas"
category: "Table manipulation"
url: "https://dax.guide/treatas/"
source: "dax.guide"
重要度:
难度:
---

# TREATAS DAX Function (Table manipulation)

Treats the columns of the input table as columns from other tables. For each column, filters out any values that are not present in its respective output column.

## Syntax

TREATAS ( <Expression>, <ColumnName> [, <ColumnName> [, … ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Expression |  | The expression that generates the set of columns to be remapped. |
| ColumnName | Repeatable | The name of the output column. |

## Return values

Table An entire table or a table with one or more columns.

A table that contains all the rows in column(s) that are also in Expression.

## Remarks

TREATAS assigns the data lineage of the columns returned by the expression using the columns in the following arguments. The result can be assigned to a variable, because TREATAS is not a filter modifier. The first argument must be a table expression.

**The TREATAS function works in Excel since version 1809.** However, the function is not reported by IntelliSense and it might be not supported in Excel by Microsoft, yet.

[» 9 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  TREATAS can be used as an alternative syntax to apply 

--  a filter in CALCULATE/CALCULATETABLE

DEFINE

    MEASURE Sales[Sales Trendy Colors] =

        CALCULATE ( 

            [Sales Amount], 

            'Product'[Color] IN { "Red", "White", "Blue" } 

        )

    MEASURE Sales[Sales Trendy Colors 2] =

        CALCULATE (

            [Sales Amount],

            TREATAS ( { "Red", "White", "Blue" }, 'Product'[Color] )

        )

EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Brand],

    "Sales Trendy Colors", [Sales Trendy Colors],

    "Sales Trendy Colors 2", [Sales Trendy Colors 2]

)

```

| Brand | Sales Trendy Colors | Sales Trendy Colors 2 |
| --- | --- | --- |
| Contoso | 2,560,661.40 | 2,560,661.40 |
| Wide World Importers | 809,002.39 | 809,002.39 |
| Northwind Traders | 557,579.98 | 557,579.98 |
| Adventure Works | 952,982.71 | 952,982.71 |
| Southridge Video | 128,714.66 | 128,714.66 |
| Litware | 1,059,954.76 | 1,059,954.76 |
| Fabrikam | 1,959,282.72 | 1,959,282.72 |
| Proseware | 963,930.44 | 963,930.44 |
| A. Datum | 34,852.00 | 34,852.00 |
| The Phone Company | 224,400.54 | 224,400.54 |
| Tailspin Toys | 123,785.02 | 123,785.02 |

```dax


--  TREATAS changes the data lineage of a table and it is

--  used to convert values to the desired filtering column.

DEFINE

    MEASURE Sales[NumOfCustomersInStoreCity] =

        VAR StoreCities = VALUES ( Store[City] )

        RETURN

            CALCULATE (

                COUNTROWS ( Customer ),

                TREATAS ( StoreCities, Customer[City] )

            )

    MEASURE Sales[NumOfCustomersInStoreCountry] =

        VAR StoreCountries = VALUES ( Store[CountryRegion] )

        RETURN

            CALCULATE (

                COUNTROWS ( Customer ),

                TREATAS ( StoreCountries, Customer[CountryRegion] )

            )

EVALUATE

SELECTCOLUMNS (

    VALUES ( Store[Continent] ),

    "Continent", Store[Continent],

    "NumOfStores", CALCULATE ( COUNTROWS ( Store ) ),

    "NumOfCustomersInStoreCity", [NumOfCustomersInStoreCity],

    "NumOfCustomersInStoreCountry", [NumOfCustomersInStoreCountry]

)

```

| Continent | NumOfStores | NumOfCustomersInStoreCity | NumOfCustomersInStoreCountry |
| --- | --- | --- | --- |
| North America | 209 | 922 | 9,665 |
| Europe | 54 | 1,181 | 5,546 |
| Asia | 41 | 133 | 3,658 |
| (Blank) | (Blank) | (Blank) | (Blank) |

```dax


--  TREATAS can be used with tables with multiple columns,

--  in that case you need to provide the new lineage for each 

--  column of the table.

DEFINE

    MEASURE Sales[NumOfCustomersInStoreCity] =

        VAR StoreCities = SUMMARIZE ( Store, Store[CountryRegion], Store[City] )

        RETURN

            CALCULATE (

                COUNTROWS ( Customer ),

                TREATAS ( StoreCities, Customer[CountryRegion], Customer[City] )

            )

    MEASURE Sales[NumOfCustomersInStoreCountry] =

        VAR StoreCountries = VALUES ( Store[CountryRegion] )

        RETURN

            CALCULATE (

                COUNTROWS ( Customer ),

                TREATAS ( StoreCountries, Customer[CountryRegion] )

            )

EVALUATE

SELECTCOLUMNS (

    VALUES ( Store[Continent] ),

    "Continent", Store[Continent],

    "NumOfStores", CALCULATE ( COUNTROWS ( Store ) ),

    "NumOfCustomersInStoreCity", [NumOfCustomersInStoreCity],

    "NumOfCustomersInStoreCountry", [NumOfCustomersInStoreCountry]

)

```

| Continent | NumOfStores | NumOfCustomersInStoreCity | NumOfCustomersInStoreCountry |
| --- | --- | --- | --- |
| North America | 209 | 922 | 9,665 |
| Europe | 54 | 1,181 | 5,546 |
| Asia | 41 | 133 | 3,658 |
| (Blank) | (Blank) | (Blank) | (Blank) |

## Related articles

Learn more about TREATAS in the following articles:

- [**Propagating filters using TREATAS in DAX**](https://www.sqlbi.com/articles/propagate-filters-using-treatas-in-dax/)

  This article describes how to create a virtual relationship in DAX using the TREATAS function, which is more efficient than approaches based on INTERSECT or FILTER. [» Read more](https://www.sqlbi.com/articles/propagate-filters-using-treatas-in-dax/)
- [**Understanding data lineage in DAX**](https://www.sqlbi.com/articles/understanding-data-lineage-in-dax/)

  Data lineage is a DAX feature so well-implemented that most developers use it without knowing about its existence. This article describes the data lineage and how it can help producing better DAX code. [» Read more](https://www.sqlbi.com/articles/understanding-data-lineage-in-dax/)
- [**Physical and Virtual Relationships in DAX**](https://www.sqlbi.com/articles/physical-and-virtual-relationships-in-dax/)

  DAX calculations can leverage relationships present in the data model, but you can obtain the same result without physical relationships, applying equivalent filters using specific DAX patterns. This article show a more efficient technique to apply virtual relationships in DAX expressions. [» Read more](https://www.sqlbi.com/articles/physical-and-virtual-relationships-in-dax/)
- [**Strong and weak relationships in Power BI**](https://www.sqlbi.com/articles/strong-and-weak-relationships-in-power-bi/)

  This article describes what weak relationships are and the differences between strong and weak relationship in Power BI and DAX. [» Read more](https://www.sqlbi.com/articles/strong-and-weak-relationships-in-power-bi/)
- [**Relationships in Power BI and Tabular models**](https://www.sqlbi.com/articles/relationships-in-power-bi-and-tabular-models/)

  This article describes the types of relationships available in Power BI and Analysis Services, clarifying the differences in cardinality and filter propagation of physical relationships. [» Read more](https://www.sqlbi.com/articles/relationships-in-power-bi-and-tabular-models/)
- [**Using tuple syntax in DAX expressions**](https://www.sqlbi.com/articles/using-tuple-syntax-in-dax-expressions/)

  This article describes the use of the tuple syntax in DAX expressions to simplify comparisons involving two or more columns. [» Read more](https://www.sqlbi.com/articles/using-tuple-syntax-in-dax-expressions/)
- [**Preparing a data model for Sankey Charts in Power BI**](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)

  This article describes how to correctly shape a data model and prepare data to use a Sankey Chart as a funnel, considering events related to a customer (contact, trial, subscription, renewal, and others). [» Read more](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)
- [**Computing MTD, QTD, YTD in Power BI for the current period**](https://www.sqlbi.com/articles/computing-mtd-qtd-ytd-in-power-bi-for-the-current-period/)

  This article describes how to use the DAX time intelligence calculations applied to the latest period available in the data, also known as the “current” period. [» Read more](https://www.sqlbi.com/articles/computing-mtd-qtd-ytd-in-power-bi-for-the-current-period/)
- [**DAX limitations with inactive relationships and row-level security (RLS)**](https://www.sqlbi.com/articles/dax-limitations-with-inactive-relationships-and-row-level-security-rls/)

  When you apply row-level security to a semantic model, there are limitations in using the USERELATIONSHIP function. This article shows the issues, provides a workaround, and its restrictions. [» Read more](https://www.sqlbi.com/articles/dax-limitations-with-inactive-relationships-and-row-level-security-rls/)

## Related functions

Other related functions are:

- [[INTERSECT]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/treatas-function](https://docs.microsoft.com/en-us/dax/treatas-function?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
