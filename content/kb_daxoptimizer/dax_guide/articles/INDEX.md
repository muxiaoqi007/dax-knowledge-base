---
title: "INDEX"
function: "index"
category: "Filter"
url: "https://dax.guide/index/"
source: "dax.guide"
重要度:
难度:
---

# INDEX DAX Function (Filter)

Retrieves a row at an absolute position (specified by the position parameter) within the specified partition sorted by the specified order or on the axis specified.

## Syntax

INDEX ( <Position> [, <Relation>] [, <OrderBy>] [, <Blanks>] [, <PartitionBy>] [, <MatchBy>] [, <Reset>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Position |  | The absolute position (1-based) from which to obtain the data:   - *Position* is positive: 1 is the first row, 2 is the second row, etc. - *Position* is negative: -1 is the last row, -2 is the second last row, etc.   When *Position* is out of the boundary, or zero, or [BLANK](https://dax.guide/blank/)(), INDEX will return an empty table. It can be any DAX expression that returns a scalar value. |
| Relation | Optional | A table expression from which the output is returned.  If specified, all columns in *OrderBy* and *PartitionBy* must come from it.  If omitted:   - *OrderBy* must be explicitly specified. - All *OrderBy* and *PartitionBy* columns must come from a single table. - Defaults to [ALLSELECTED](https://dax.guide/allselected/)() of all columns in *OrderBy* and *PartitionBy*. |
| OrderBy | Optional | An [ORDERBY](https://dax.guide/orderby/)() clause containing the columns that define how each partition is sorted.  If omitted:   - *Relation* must be explicitly specified. - Defaults to ordering by every column in *Relation*. |
| Blanks | Optional | An enumeration that defines how to handle blank values when sorting.  This parameter is reserved for future use.  Currently, the only supported value is KEEP (default), where the behavior for numerical/date values is blank values are ordered between zero and negative values. The behavior for strings is blank values are ordered before all strings, including empty strings. |
| PartitionBy | Optional | A [PARTITIONBY](https://dax.guide/partitionby/)() clause containing the columns that define how *Relation* is partitioned.  If omitted, *Relation* is treated as a single partition. |
| MatchBy | Optional | Columns that define how the current row is identified. |
| Reset | Optional | Specifies how the calculation restarts. Valid values are: None, LowestParent, HighestParent, or an integer. |

## Return values

Table An entire table or a table with one or more columns.

A row at an absolute position.

## Remarks

Each *PartitionBy* column must have a corresponding outer value to help define the “current partition” on which to operate, with the following behavior:

- If there is exactly one corresponding outer column, its value is used.
- If there is no corresponding outer column:
  - INDEX will first determine all *PartitionBy* columns that have no corresponding outer column.
  - For every combination of existing values for these columns in INDEX’s parent context, INDEX is evaluated and a row is returned.
  - INDEX’s final output is a union of these rows.
- If there is more than one corresponding outer column, an error is returned.

If the non-volatile columns specified within *OrderBy* and *PartitionBy* cannot uniquely identify every row in *Relation*:

- INDEX will try to find the least number of additional columns required to uniquely identify every row.
- If such columns can be found, INDEX will automatically append these new columns to *OrderBy*, and each partition is sorted using this new set of OrderBy columns.
- If such columns cannot be found, an error is returned.

An empty table is returned if:

- The corresponding outer value of a PartitionBy column does not exist within *Relation*.
- The *Position* value refers to a position that does not exist within the partition.

If INDEX is used within a calculated column defined on the same table as *Relation* and *OrderBy* is omitted, an error is returned.

[» 2 related articles](#articles)  
[» 4 related functions](#alt)  

## Examples

```dax


EVALUATE

VAR BrandsAndSales =

    ADDCOLUMNS (

        ALL ( 'Product'[Brand] ),

        "@Sales", [Sales Amount]

    )

RETURN

    INDEX (

        2,

        BrandsAndSales,

        ORDERBY ( [@Sales], DESC )

    )

```

| Brand | @Sales |
| --- | --- |
| Contoso | 2,227,244.32 |

```dax


EVALUATE

SUMMARIZECOLUMNS (

    Product[Brand],

    'Date'[Year],

    "Sales", [Sales Amount],

    "Sales First Year",

        CALCULATE (

            [Sales Amount],

            INDEX (

                1,

                ORDERBY ( 'Date'[Year] )

            )

        )

)

```

| Product[Brand] | Date[Year] | Sales | Sales First Year |
| --- | --- | --- | --- |
| Contoso | 2,017 | 511,810.85 | 511,810.85 |
| Contoso | 2,018 | 868,179.21 | 511,810.85 |
| Contoso | 2,019 | 707,010.55 | 511,810.85 |
| Contoso | 2,020 | 140,243.70 | 511,810.85 |
| Wide World Importers | 2,017 | 483,427.60 | 483,427.60 |
| Wide World Importers | 2,018 | 737,726.73 | 483,427.60 |
| Wide World Importers | 2,019 | 458,943.97 | 483,427.60 |
| Wide World Importers | 2,020 | 116,832.70 | 483,427.60 |
| Northwind Traders | 2,017 | 21,598.14 | 21,598.14 |
| Northwind Traders | 2,018 | 64,737.97 | 21,598.14 |
| … | … | … | … |

## Related articles

Learn more about INDEX in the following articles:

- [**Introducing window functions in DAX**](https://www.sqlbi.com/articles/introducing-window-functions-in-dax/)

  In December 2022, DAX was enriched with window functions: INDEX, OFFSET, and WINDOW. This article introduces the syntax and the basic functionalities of these new features. [» Read more](https://www.sqlbi.com/articles/introducing-window-functions-in-dax/)
- [**SQLBI+ updates in May 2023**](https://www.sqlbi.com/blog/marco/2024/05/27/sqlbi-updates-in-may-2023/)

  In 2023, we released the first draft of the Window functions in DAX whitepaper as part of SQLBI+. Since then, we have released a few updates and are now glad to announce the availability of the related 3-hour video course… [» Read more](https://www.sqlbi.com/blog/marco/2024/05/27/sqlbi-updates-in-may-2023/)

## Related functions

Other related functions are:

- [OFFSET](https://dax.guide/offset/)
- [WINDOW](https://dax.guide/window/)
- [ORDERBY](https://dax.guide/orderby/)
- [PARTITIONBY](https://dax.guide/partitionby/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Daniil Maslyuk

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/index-function-dax](https://learn.microsoft.com/en-us/dax/index-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
