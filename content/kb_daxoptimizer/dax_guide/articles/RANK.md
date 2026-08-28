---
title: "RANK"
function: "rank"
url: "https://dax.guide/rank/"
source: "dax.guide"
重要度:
难度:
---

# RANK DAX Function

Returns the rank for the current context within the specified partition sorted by the specified order or on the axis specified.

## Syntax

RANK ( [<Ties>] [, <Relation>] [, <OrderBy>] [, <Blanks>] [, <PartitionBy>] [, <MatchBy>] [, <Reset>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Ties | Optional | DENSE or SKIP.  Defines how to handle the ranking when two or more rows are tied. If omitted, SKIP is the default. |
| Relation | Optional | A table expression from which the output row is returned.  If specified, all columns in *OrderBy* and *PartitionBy* must come from it.  If omitted, it defaults to [[ALLSELECTED]]() of all columns in *OrderBy* and *PartitionBy*, and *OrderBy* must be explicitly specified. |
| OrderBy | Optional | An [[ORDERBY]]() clause containing the columns that define how each partition is sorted.  If omitted, *Relation* must be explicitly specified, and it orders by every column in *Relation* that is not already specified in *PartitionBy*. |
| Blanks | Optional | An enumeration that defines how to handle blank values when sorting.  The supported values are:   - DEFAULT (the default value), where the behavior for numerical values is blank values are ordered between zero and negative values. The behavior for strings is blank values are ordered before all strings, including empty strings. - [[FIRST]], blanks are always ordered on the beginning, regardless of ascending or descending sorting order. - [[LAST]], blanks are always ordered on the end, regardless of ascending or descending sorting order.   Note, when blanks parameter and blanks in [[ORDERBY]]() function on individual expression are both specified, blanks on individual *OrderBy* expression takes priority for the relevant *OrderBy* expression, and *OrderBy* expressions without blanks being specified will honor blanks parameter on parent Window function. |
| PartitionBy | Optional | A [[PARTITIONBY]]() clause containing the columns that define how *Relation* is partitioned.  If omitted, *Relation* is treated as a single partition. |
| MatchBy | Optional | A [[MATCHBY]]() clause containing the columns that define how to match data and identify the current row. |
| Reset | Optional | Specifies how the calculation restarts. Valid values are: None, LowestParent, HighestParent, or an integer. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

The rank number for the current context.

[» 4 related articles](#articles)  

## Examples

```dax


DEFINE

    MEASURE Sales[Rounded Sales] =

        MROUND ( [Sales Amount], 400000 )

    MEASURE Sales[Rank] =

        VAR SourceTable =

            ADDCOLUMNS ( ALLSELECTED ( Product[Brand] ), "@Amt", [Rounded Sales] )

        VAR Result =

            RANK ( DENSE, SourceTable, ORDERBY ( [@Amt], DESC, Product[Brand], ASC ) )

        RETURN

            Result



EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Category],

    'Product'[Brand],

    TREATAS ( { "Audio", "Computers" }, 'Product'[Category] ),

    "Sales Amount", [Sales Amount],

    "Rank", [Rank]

)

ORDER BY

    'Product'[Category],

    [Rank] ASC



```

| Category | Brand | Sales Amount | Rank |
| --- | --- | --- | --- |
| Audio | Contoso | 170,194.00 | 1 |
| Audio | Northwind Traders | 60,942.07 | 2 |
| Audio | Wide World Importers | 153,382.09 | 3 |
| Computers | Proseware | 1,905,253.77 | 1 |
| Computers | Adventure Works | 1,646,596.85 | 2 |
| Computers | Wide World Importers | 1,422,852.03 | 3 |
| Computers | Contoso | 1,054,179.83 | 4 |
| Computers | Fabrikam | 550,289.94 | 5 |
| Computers | Southridge Video | 162,376.31 | 6 |

## Related articles

Learn more about RANK in the following articles:

- [**Introducing the RANK window function in DAX**](https://www.sqlbi.com/articles/introducing-the-rank-window-function-in-dax/)

  RANK is a new DAX function to rank items based on multiple columns. This article introduces the RANK function and its differences with RANKX. [» Read more](https://www.sqlbi.com/articles/introducing-the-rank-window-function-in-dax/)
- [**Preparing a data model for Sankey Charts in Power BI**](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)

  This article describes how to correctly shape a data model and prepare data to use a Sankey Chart as a funnel, considering events related to a customer (contact, trial, subscription, renewal, and others). [» Read more](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)
- [**SQLBI+ updates in May 2023**](https://www.sqlbi.com/blog/marco/2024/05/27/sqlbi-updates-in-may-2023/)

  In 2023, we released the first draft of the Window functions in DAX whitepaper as part of SQLBI+. Since then, we have released a few updates and are now glad to announce the availability of the related 3-hour video course… [» Read more](https://www.sqlbi.com/blog/marco/2024/05/27/sqlbi-updates-in-may-2023/)
- [**Using RANK instead of RANKX in DAX**](https://www.sqlbi.com/articles/using-rank-instead-of-rankx-in-dax/)

  Should you use RANK or stick with RANKX? In which scenarios is one better than the other? This article provides an in-depth analysis to help readers make informed choices. [» Read more](https://www.sqlbi.com/articles/using-rank-instead-of-rankx-in-dax/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/dax/rank-function-dax](https://learn.microsoft.com/dax/rank-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
