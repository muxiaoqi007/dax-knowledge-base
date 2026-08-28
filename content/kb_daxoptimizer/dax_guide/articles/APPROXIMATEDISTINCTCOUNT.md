---
title: "APPROXIMATEDISTINCTCOUNT"
function: "approximatedistinctcount"
category: "Aggregation"
url: "https://dax.guide/approximatedistinctcount/"
source: "dax.guide"
重要度:
难度:
---

# APPROXIMATEDISTINCTCOUNT DAX Function (Aggregation)

Returns an estimated count of the unique values in a column. This function invokes a corresponding aggregation operation in the data source, optimized for query performance but with slightly reduced accuracy. You can use APPROXIMATEDISTINCTCOUNT with the following data sources: Azure SQL, Azure SQL Data Warehouse, BigQuery, Databricks, and Snowflake. Note that this function requires DirectQuery mode. Import mode and dual storage mode are not supported.

## Syntax

APPROXIMATEDISTINCTCOUNT ( <ColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName |  | The column for which the distinct values are counted. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

The approximate number of distinct values in ColumnName.

## Remarks

The SQL query generated uses the [APPROX\_COUNT\_DISTINCT](https://docs.microsoft.com/en-us/sql/t-sql/functions/approx-count-distinct-transact-sql?view=sql-server-2017) in Transact-SQL to obtain the result.

[» 2 related functions](#alt)  

## Examples

```dax


--  APPROXIMATEDISTINCTCOUNT can be used only in models using DirectQuery

--  on Azure SQL or Azure SQL Data Warehouse.

--  It uses the faster (but less accurate) version of DISTINCT available 

--  on these engines to compute distinct counts: APPROX_COUNT_DISTINCT 

--  The function implementation guarantees up to a 2% error rate within 

--  a 97% probability.

DEFINE

    MEASURE Sales[#Prods] = DISTINCTCOUNT ( Sales[ProductKey] )

    MEASURE Sales[#App Prods] = APPROXIMATEDISTINCTCOUNT ( Sales[ProductKey] )

EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Calendar Year],

    TREATAS ( { "CY 2007", "CY 2008", "CY 2009" }, 'Date'[Calendar Year] ),

    "#Prods",       [#Prods],

    "#App Prods",   [#App Prods]

)

```

## Related functions

Other related functions are:

- [[DISTINCTCOUNT]]
- [[DISTINCTCOUNTNOBLANK]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/approximate-distinctcount-function-dax](https://docs.microsoft.com/en-us/dax/approximate-distinctcount-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
