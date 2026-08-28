---
title: "OFFSET"
function: "offset"
category: "Filter"
url: "https://dax.guide/offset/"
source: "dax.guide"
重要度:
难度:
---

# OFFSET DAX Function (Filter)

Retrieves a single row from a relation by moving a number of rows within the specified partition, sorted by the specified order or on the axis specified.

## Syntax

OFFSET ( <Delta> [, <Relation>] [, <OrderBy>] [, <Blanks>] [, <PartitionBy>] [, <MatchBy>] [, <Reset>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Delta |  | The number of rows before (negative value) or after (positive value) the current row from which to obtain the data. It can be any DAX expression that returns a scalar value. |
| Relation  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

 Optional | A table expression from which the output row is returned.  If specified, all columns in *OrderBy* and *PartitionBy* must come from it.  If omitted:   - *OrderBy* must be explicitly specified. - All *OrderBy* and *PartitionBy* columns must be fully qualified and come from a single table. - Defaults to [[ALLSELECTED]]() of all columns in *OrderBy* and *PartitionBy*. || OrderBy  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression | Optional | An [[ORDERBY]]() clause containing the columns that define how each partition is sorted.  If omitted:   - *Relation* must be explicitly specified. - Defaults to ordering by every column in *Relation* that is not already specified in *PartitionBy*. |
| Blanks | Optional | An enumeration that defines how to handle blank values when sorting.  This parameter is reserved for future use.  Currently, the only supported value is KEEP (default), where the behavior for numerical/date values is blank values are ordered between zero and negative values. The behavior for strings is blank values are ordered before all strings, including empty strings. |
| PartitionBy | Optional | A [[PARTITIONBY]]() clause containing the columns that define how *Relation* is partitioned.  If omitted, *Relation* is treated as a single partition. |
| MatchBy | Optional | Columns that define how the current row is identified. |
| Reset | Optional | Specifies how the calculation restarts. Valid values are: None, LowestParent, HighestParent, or an integer. |

## Return values

Table An entire table or a table with one or more columns.

One or more rows from *Relation*.

## Remarks

Each *OrderBy* and *PartitionBy* column must have a corresponding outer value to help define the current row on which to operate, with the following behavior:

- If there is exactly one corresponding outer column, its value is used.
- If there is no corresponding outer column, then:
  - OFFSET will first determine all *OrderBy* and *PartitionBy* columns that have no corresponding outer column.
  - For every combination of existing values for these columns in OFFSET’s parent context, OFFSET is evaluated and a row is returned.
  - OFFSET’s final output is a union of these rows.
- If there is more than one corresponding outer column, an error is returned.

If the non-volatile columns specified within *OrderBy* and *PartitionBy* can’t uniquely identify every row in *Relation*, then:

- OFFSET will try to find the least number of additional columns required to uniquely identify every row.
- If such columns can be found, OFFSET will automatically append these new columns to *OrderBy*, and each partition is sorted using this new set of OrderBy columns.
- If such columns cannot be found, an error is returned.

An empty table is returned if:

- The corresponding outer value of an OrderBy or PartitionBy column does not exist within *Relation*.
- The *Delta* value causes a shift to a row that does not exist within the partition.

If OFFSET is used within a calculated column defined on the same table as *Relation*, and *OrderBy* is omitted, an error is returned.

[» 5 related articles](#articles)  
[» 4 related functions](#alt)  

## Examples

```dax


DEFINE

    MEASURE Sales[Prev Year Sales] =

        CALCULATE (

            [Sales Amount],

            OFFSET (

                -1,

                ALLSELECTED ( 'Date'[Year] ),

                ORDERBY ( 'Date'[Year], ASC )

            )

        )

EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Year],

    TREATAS ( { 2019, 2020 }, 'Date'[Year] ),

    "Sales Amount", [Sales Amount],

    "Prev Year Sales", [Prev Year Sales]

)

```

| Date[Year] | Sales Amount | Prev Year Sales |
| --- | --- | --- |
| 2,019 | 3,550,194.76 | (Blank) |
| 2,020 | 769,835.80 | 3,550,194.76 |

```dax


DEFINE

    TABLE ProcessData =

        -- Create a table with index column and "process" values from the DAX Guide db

        SELECTCOLUMNS (

            SUMMARIZECOLUMNS (

                'Date'[Calendar Year Month Number],

                FILTER (

                    ALL ( 'Date'[Calendar Year Month Number] ),

                    'Date'[Calendar Year Month Number] < 200804

                ),

                "Amt", [Sales Amount]

            ),

            "Index", [Calendar Year Month Number],

            "Value", [Amt]

        )

    -- Basic sum measure of the value column

    MEASURE ProcessData[SumValue] =

        SUM ( ProcessData[Value] )

    -- Average value of the data

    VAR MeanValue =

        AVERAGE ( ProcessData[Value] )

    -- Use the OFFSET function to first calculate the absolute differences 

    -- between two consequent values (a.k.a. Moving Range) 

    -- https://en.wikipedia.org/wiki/Shewhart_individuals_control_chart

    MEASURE ProcessData[MovingRange] =

        VAR PreviousValue =

            CALCULATE (

                [SumValue],

                OFFSET (

                    -1,

                    ORDERBY ( ProcessData[Index] )

                )

            )

        RETURN

            IF (

                NOT ISBLANK ( PreviousValue ),

                ABS ( [SumValue] - PreviousValue )

            )

    -- Derive process standard deviation or "sigma" based on the Moving Range

    VAR Sigma =

        VAR MeanMovingRange =

            CALCULATE (

                AVERAGEX (

                    ALL ( ProcessData[Index] ),

                    [MovingRange]

                )

            )

        RETURN

            MeanMovingRange

                * SQRTPI ( 1 ) / 2

    -- Use the WINDOW function to implement the WE Rule 2 for process instability detection:

    -- "Two out of three consecutive points fall beyond the 2σ-limit (in zone A or beyond), 

    -- on the same side of the centerline"

    -- https://en.wikipedia.org/wiki/Western_Electric_rules

    MEASURE ProcessData[Rule2Test] =

        VAR ZoneARowsPositive =

            CALCULATE (

                COUNTROWS ( ProcessData ),

                FILTER (

                    WINDOW (

                        -2,

                        0,

                        ORDERBY ( ProcessData[Index] )

                    ),

                    [SumValue] >= MeanValue + 2 * Sigma

                )

            )

        VAR ZoneARowsNegative =

            CALCULATE (

                COUNTROWS ( ProcessData ),

                FILTER (

                    WINDOW (

                        -2,

                        0,

                        ORDERBY ( ProcessData[Index] )

                    ),

                    [SumValue] <= MeanValue - 2 * Sigma

                )

            )

        RETURN

            -- Flag the rows where the rule is violated

            IF (

                ZoneARowsPositive >= 2 || ZoneARowsNegative >= 2,

                1

            )



EVALUATE

SUMMARIZECOLUMNS (

    ProcessData[Index],

    "SumValue", [SumValue],

    "Process Mean", MeanValue,

    "-2 σ", MeanValue - 2 * Sigma,

    "+2 σ", MeanValue + 2 * Sigma,

    "Rule 2 Indicator", [Rule2Test]

)



```

| Index | SumValue | Process Mean | -2 σ | +2 σ | Rule 2 Indicator |
| --- | --- | --- | --- | --- | --- |
| 200,701 | 794,248.24 | 875,088.76 | 685,032.06 | 1,065,145.45 | (Blank) |
| 200,702 | 891,135.91 | 875,088.76 | 685,032.06 | 1,065,145.45 | (Blank) |
| 200,703 | 961,289.24 | 875,088.76 | 685,032.06 | 1,065,145.45 | (Blank) |
| 200,704 | 1,128,104.82 | 875,088.76 | 685,032.06 | 1,065,145.45 | (Blank) |
| 200,705 | 936,192.74 | 875,088.76 | 685,032.06 | 1,065,145.45 | (Blank) |
| 200,706 | 982,304.46 | 875,088.76 | 685,032.06 | 1,065,145.45 | (Blank) |
| 200,707 | 922,542.98 | 875,088.76 | 685,032.06 | 1,065,145.45 | (Blank) |
| 200,708 | 952,834.59 | 875,088.76 | 685,032.06 | 1,065,145.45 | (Blank) |
| 200,709 | 1,009,868.98 | 875,088.76 | 685,032.06 | 1,065,145.45 | (Blank) |
| 200,710 | 914,273.54 | 875,088.76 | 685,032.06 | 1,065,145.45 | (Blank) |
| 200,711 | 825,601.87 | 875,088.76 | 685,032.06 | 1,065,145.45 | (Blank) |
| 200,712 | 991,548.75 | 875,088.76 | 685,032.06 | 1,065,145.45 | (Blank) |
| 200,801 | 656,766.69 | 875,088.76 | 685,032.06 | 1,065,145.45 | (Blank) |
| 200,802 | 600,080.00 | 875,088.76 | 685,032.06 | 1,065,145.45 | 1 |
| 200,803 | 559,538.52 | 875,088.76 | 685,032.06 | 1,065,145.45 | 1 |

## Related articles

Learn more about OFFSET in the following articles:

- [**Introducing window functions in DAX**](https://www.sqlbi.com/articles/introducing-window-functions-in-dax/)

  In December 2022, DAX was enriched with window functions: INDEX, OFFSET, and WINDOW. This article introduces the syntax and the basic functionalities of these new features. [» Read more](https://www.sqlbi.com/articles/introducing-window-functions-in-dax/)
- [**Preparing a data model for Sankey Charts in Power BI**](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)

  This article describes how to correctly shape a data model and prepare data to use a Sankey Chart as a funnel, considering events related to a customer (contact, trial, subscription, renewal, and others). [» Read more](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)
- [**Introducing VISUAL SHAPE for visual calculations in Power BI**](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/)

  This article introduces the VISUAL SHAPE clause, which defines a hierarchical structure for a table used in visual calculations. [» Read more](https://www.sqlbi.com/articles/introducing-visual-shape-for-visual-calculations-in-power-bi/)
- [**Understanding apply semantics for window functions in DAX**](https://www.sqlbi.com/articles/understanding-apply-semantics-for-window-functions-in-dax/)

  This article explains the unique behavior of apply semantics: a new way of computing table expressions when multiple rows are selected in DAX window functions. [» Read more](https://www.sqlbi.com/articles/understanding-apply-semantics-for-window-functions-in-dax/)
- [**SQLBI+ updates in May 2023**](https://www.sqlbi.com/blog/marco/2024/05/27/sqlbi-updates-in-may-2023/)

  In 2023, we released the first draft of the Window functions in DAX whitepaper as part of SQLBI+. Since then, we have released a few updates and are now glad to announce the availability of the related 3-hour video course… [» Read more](https://www.sqlbi.com/blog/marco/2024/05/27/sqlbi-updates-in-may-2023/)

## Related functions

Other related functions are:

- [[INDEX]]
- [[WINDOW]]
- [[ORDERBY]]
- [[PARTITIONBY]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Ville-Pietari Louhiala

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/offset-function-dax](https://learn.microsoft.com/en-us/dax/offset-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
