---
title: "COLUMNSTATISTICS"
function: "columnstatistics"
category: "Information"
url: "https://dax.guide/columnstatistics/"
source: "dax.guide"
重要度:
难度:
---

# COLUMNSTATISTICS DAX Function (Information)

Provides statistics regarding every column in every table in the model.

## Syntax

COLUMNSTATISTICS ( )

This expression has no parameters.

## Return values

Table An entire table or a table with one or more columns.

One row for each column visible in the model to the current user, with the following data: *Table Name*, *Column Name*, *Min*, *Max*, *Cardinality*, *Max Length*.

## Remarks

In the following remarks, “column” relates to the result of the function and “attribute” to a column in the data model.

The COLUMNSTATISTICS function returns the attributes and values visible to the user considering the security roles. Different users can get different results querying the same model because of different security profiles.

The *Cardinality* column provides the number of unique values for each attribute in the data model. For rows where *Column Name* begins with “RowNumber”, the value of *Cardinality* corresponds to the number of rows in the table. When supported in DirectQuery, the value of *Cardinality* is approximate, whereas it is the exact number for tables imported in memory. If not supported, then the *Cardinality* is blank.

The *Min* and *Max* columns show the minimum and maximum values visible in the attribute.

The *Max Length* column shows the maximum length of strings visible in the attribute.

When used with a DirectQuery data source over SQL Server, it is compatible only with SQL Server 2019 or later version, because internally it uses APPROX\_COUNT\_DISTINCT to compute the *Cardinality* value.

## Examples

```dax


EVALUATE COLUMNSTATISTICS()

```

| Table Name | Column Name | Min | Max | Cardinality | Max Length |
| --- | --- | --- | --- | --- | --- |
| Customer | RowNumber-2662979B-1795-4F74-8F37-6A1BA8059B61 | (Blank) | (Blank) | 18,869 | (Blank) |
| Customer | CustomerKey | 1 | 19,145 | 18,869 | (Blank) |
| Customer | Customer Code | 11000 | CS952 | 18,869 | 5 |
| Customer | Title | Mr. | Sr. | 6 | 4 |
| Customer | Name |  | Zukowski, Jake | 18,401 | 27 |
| Customer | Birth Date | 1910-08-13T00:00:00 | 1980-12-26T00:00:00 | 8,253 | (Blank) |
| … | … | . | . | . | . |
| Product | Unit Price | 1 | 3,200 | 426 | (Blank) |
| Product | Available Date | 2004-01-01T00:00:00 | 2009-07-21T00:00:00 | 355 | (Blank) |
| Product | Subcategory | Air Conditioners | Water Heaters | 32 | 32 |
| Product | Category | Audio | TV and Video | 8 | 29 |
| Currency | RowNumber-2662979B-1795-4F74-8F37-6A1BA8059B61 | (Blank) | (Blank) | 28 | (Blank) |
| Currency | CurrencyKey | 1 | 28 | 28 | (Blank) |
| Currency | Currency Code | AMD | USD | 28 | 3 |
| Currency | Currency | Armenian Dram | US Dollar | 28 | 18 |

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/columnstatistics-function-dax](https://learn.microsoft.com/en-us/dax/columnstatistics-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
