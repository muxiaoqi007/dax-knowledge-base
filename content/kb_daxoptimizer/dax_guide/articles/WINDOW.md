---
title: "WINDOW"
function: "window"
category: "Filter"
url: "https://dax.guide/window/"
source: "dax.guide"
重要度:
难度:
---

# WINDOW DAX Function (Filter)

Retrieves a range of rows within the specified partition, sorted by the specified order or on the axis specified.

## Syntax

WINDOW ( <From> [, <FromType>], <To> [, <ToType>] [, <Relation>] [, <OrderBy>] [, <Blanks>] [, <PartitionBy>] [, <MatchBy>] [, <Reset>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| From |  | Indicates where the window starts. It can be any DAX expression that returns a scalar value.  The behavior depends on the *FromType* parameter:   - If *FromType* is REL, the number of rows to go back (negative value) or forward (positive value) from the current row to get the first row in the window. - If *FromType* is [[ABS]], and *From* is positive, then it’s the position of the start of the window from beginning of the partition. Indexing is 1-based. For example, 1 means window starts from the beginning of the partition. If *From* is negative, then it’s the position of the start of the window from the end of the partition. -1 means the last row in the partition. |
| FromType | Optional | Modifies behavior of the *From* parameter. Possible values are [[ABS]] (absolute) and REL (relative). Default is REL. |
| To |  | Indicates the end of the window. The last row is included in the window.  It can be any DAX expression that returns a scalar value.  The behavior depends on the *ToType* parameter:   - If *ToType* is REL, the number of rows to go back (negative value) or forward (positive value) from the current row to get the last row in the window. - If *ToType* is [[ABS]], and *To* is positive, then it’s the position of the end of the window from beginning of the partition. Indexing is 1-based. For example, 1 means window starts from the beginning of the partition. If *To* is negative, then it’s the position of the end of the window from the end of the partition. -1 means the last row in the partition. |
| ToType | Optional | Modifies behavior of the *To* parameter. Possible values are [[ABS]] (absolute) and REL (relative). Default is REL. |
| Relation | Optional | A table expression from which the output row is returned.  If specified, all columns in *OrderBy* and *PartitionBy* must come from it.  If omitted:   - *OrderBy* must be explicitly specified. - All *OrderBy* and *PartitionBy* columns must be fully qualified and come from a single table. - Defaults to [[ALLSELECTED]]() of all columns in *OrderBy* and *PartitionBy.* |
| OrderBy | Optional | An [[ORDERBY]]() clause containing the columns that define how each partition is sorted.  If omitted:   - *Relation* must be explicitly specified. - Defaults to ordering by every column in *Relation* that is not already specified in *PartitionBy.* |
| Blanks | Optional | An enumeration that defines how to handle blank values when sorting.  This parameter is reserved for future use.  Currently, the only supported value is KEEP (default), where the behavior for numerical/date values is blank values are ordered between zero and negative values. The behavior for strings is blank values are ordered before all strings, including empty strings. |
| PartitionBy | Optional | A [[PARTITIONBY]]() clause containing the columns that define how *Relation* is partitioned. If omitted, *Relation* is treated as a single partition. |
| MatchBy | Optional | Columns that define how the current row is identified. |
| Reset | Optional | Specifies how the calculation restarts. Valid values are: None, LowestParent, HighestParent, or an integer. |

## Return values

Table An entire table or a table with one or more columns.

All rows from the window.

## Remarks

Each <orderBy> and <partitionBy> column must have a corresponding outer value to help define the current row on which to operate. If <from\_type> and <to\_type> both have value [[ABS]], then the following applies only to the <partitionBy> columns:

- If there is exactly one corresponding outer column, its value is used.
- If there is no corresponding outer column:
  - WINDOW will first determine all <orderBy> and <partitionBy> columns that have no corresponding outer column.
  - For every combination of existing values for these columns in WINDOW’s parent context, WINDOW is evaluated, and the corresponding rows is returned.
  - WINDOW final output is a union of these rows.
- If there is more than one corresponding outer column, an error is returned.

If the columns specified within *OrderBy* and *PartitionBy* cannot uniquely identify every row in *Relation,* then:

- WINDOW will try to find the least number of additional columns required to uniquely identify every row.
- If such columns can be found, WINDOW will automatically append these new columns to *OrderBy,* and each partition is sorted using this new set of OrderBy columns.
- If such columns cannot be found, an error is returned.

An empty table is returned if:

- The corresponding outer value of an *OrderBy* or *PartitionBy* column does not exist within *Relation.*
- The whole window is outside the partition, or the beginning of the window is after its ending.

If WINDOW is used within a calculated column defined on the same table as *Relation*, and *OrderBy* is omitted, an error is returned.

If the beginning of the window turns out be before the first row, then it’s set to the first row. Similarly, if the end of the window is after the last row of the partition, then it’s set to the last row.

[» 7 related articles](#articles)  
[» 4 related functions](#alt)  

## Examples

```dax


EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Year Month Number],

    'Date'[Year Month],

    "Sales Amount", [Sales Amount],

    "Sales RT",

        SUMX (

            WINDOW (

                1, ABS,

                0, REL,

                ORDERBY ( 

                    'Date'[Year Month Number], ASC,

                    'Date'[Year Month], ASC 

                )

            ),

            [Sales Amount]

        )

)

ORDER BY 'Date'[Year Month Number]

```

| Date[Year Month Number] | Date[Year Month] | Sales Amount | Sales RT |
| --- | --- | --- | --- |
| 24209 | May 2017 | 168,392.56 | 168,392.56 |
| 24210 | June 2017 | 263,600.69 | 431,993.25 |
| 24211 | July 2017 | 204,281.19 | 636,274.44 |
| 24212 | August 2017 | 312,793.50 | 949,067.94 |
| 24213 | September 2017 | 334,423.50 | 1,283,491.44 |
| 24214 | October 2017 | 402,067.05 | 1,685,558.49 |
| 24215 | November 2017 | 438,804.70 | 2,124,363.19 |
| 24216 | December 2017 | 908,941.83 | 3,033,305.02 |
| 24217 | January 2018 | 636,983.88 | 3,670,288.90 |
| 24218 | February 2018 | 788,062.88 | 4,458,351.78 |
| 24219 | March 2018 | 269,320.40 | 4,727,672.19 |
| … | … | … | … |

```dax


EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Year Month Number],

    'Date'[Year Month],

    "Sales Amount", [Sales Amount],

    "Sales RT",

        SUMX (

            WINDOW (

                1, ABS,

                0, REL,

                ORDERBY ( 

                    'Date'[Year Month Number], ASC,

                    'Date'[Year Month], ASC 

                ),

                PARTITIONBY ( 'Date'[Year] )

            ),

            [Sales Amount]

        )

)

ORDER BY 'Date'[Year Month Number]

```

| Date[Year Month Number] | Date[Year Month] | Sales Amount | Sales RT |
| --- | --- | --- | --- |
| 24209 | May 2017 | 168,392.56 | 168,392.56 |
| 24210 | June 2017 | 263,600.69 | 431,993.25 |
| 24211 | July 2017 | 204,281.19 | 636,274.44 |
| 24212 | August 2017 | 312,793.50 | 949,067.94 |
| 24213 | September 2017 | 334,423.50 | 1,283,491.44 |
| 24214 | October 2017 | 402,067.05 | 1,685,558.49 |
| 24215 | November 2017 | 438,804.70 | 2,124,363.19 |
| 24216 | December 2017 | 908,941.83 | 3,033,305.02 |
| 24217 | January 2018 | 636,983.88 | 636,983.88 |
| 24218 | February 2018 | 788,062.88 | 1,425,046.76 |
| 24219 | March 2018 | 269,320.40 | 1,694,367.17 |
| … | … | … | … |

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

Learn more about WINDOW in the following articles:

- [**Introducing window functions in DAX**](https://www.sqlbi.com/articles/introducing-window-functions-in-dax/)

  In December 2022, DAX was enriched with window functions: INDEX, OFFSET, and WINDOW. This article introduces the syntax and the basic functionalities of these new features. [» Read more](https://www.sqlbi.com/articles/introducing-window-functions-in-dax/)
- [**Using EXPAND and COLLAPSE in visual calculations**](https://www.sqlbi.com/articles/using-expand-and-collapse-in-visual-calculations/)

  This article provides examples of visual calculations where the use of EXPAND and COLLAPSE is required to obtain the correct result. [» Read more](https://www.sqlbi.com/articles/using-expand-and-collapse-in-visual-calculations/)
- [**Understanding apply semantics for window functions in DAX**](https://www.sqlbi.com/articles/understanding-apply-semantics-for-window-functions-in-dax/)

  This article explains the unique behavior of apply semantics: a new way of computing table expressions when multiple rows are selected in DAX window functions. [» Read more](https://www.sqlbi.com/articles/understanding-apply-semantics-for-window-functions-in-dax/)
- [**SQLBI+ updates in May 2023**](https://www.sqlbi.com/blog/marco/2024/05/27/sqlbi-updates-in-may-2023/)

  In 2023, we released the first draft of the Window functions in DAX whitepaper as part of SQLBI+. Since then, we have released a few updates and are now glad to announce the availability of the related 3-hour video course… [» Read more](https://www.sqlbi.com/blog/marco/2024/05/27/sqlbi-updates-in-may-2023/)
- [**Computing open orders with visual calculations in DAX**](https://www.sqlbi.com/articles/computing-open-orders-with-visual-calculations-in-dax/)

  This article describes the use of visual calculations for a scenario where they may be particularly relevant: computing open orders at the end of a time period. [» Read more](https://www.sqlbi.com/articles/computing-open-orders-with-visual-calculations-in-dax/)
- [**Account receivable aging in Power BI**](https://www.sqlbi.com/articles/account-receivable-aging-in-power-bi/)

  This article describes an Accounts Receivable Aging report in Power BI, and shows how to simplify a business problem using existing modeling patterns. [» Read more](https://www.sqlbi.com/articles/account-receivable-aging-in-power-bi/)
- [**Dynamic Pareto analysis in Power BI**](https://www.sqlbi.com/articles/dynamic-pareto-analysis-in-power-bi/)

  This article describes how to implement a dynamic Pareto calculation in Power BI based on a measure that can be selected from a slicer and dynamically filtered by other slicers in the report. [» Read more](https://www.sqlbi.com/articles/dynamic-pareto-analysis-in-power-bi/)

## Related functions

Other related functions are:

- [[INDEX]]
- [[OFFSET]]
- [[ORDERBY]]
- [[PARTITIONBY]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Ville-Pietari Louhiala

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/window-function-dax](https://learn.microsoft.com/en-us/dax/window-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
