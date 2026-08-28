---
title: "RANK.EQ"
function: "rank-eq"
category: "Statistical"
url: "https://dax.guide/rank-eq/"
source: "dax.guide"
重要度:
难度:
---

# RANK.EQ DAX Function (Statistical)

Returns the rank of a number in a column of numbers. If more than one value has the same rank, the top rank of that set of values is returned.

## Syntax

RANK.EQ ( <Value>, <ColumnName> [, <Order>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Value |  | The value to be ranked. |
| ColumnName |  | A column in which the value will be ranked. |
| Order | Optional | The order to be applied. 0/FALSE/DESC – descending; 1/TRUE/ASC – ascending. If omitted, the default is descending. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

A number indicating the rank of Value among the numbers in ColumnName.

## Remarks

The following RANK.EQ calls:

```dax


RANK.EQ ( <value>, table[column] )



RANK.EQ ( <value>, table[column], <order> )

```

correspond to the following [[RANKX]] calls:

```dax


RANKX (

    SELECTCOLUMNS ( table, table[column] ),

    table[column],

    <value>

)



RANKX (

    SELECTCOLUMNS ( table, table[column] ),

    table[column],

    <value>,

    <order>

)

```

The <value> placeholder is a DAX expression executed in the filter context where RANK.EQ is evaluated.  
The <order> placeholder is the optional order argument, which is descending by default.

[» 1 related function](#alt)  

## Examples

```dax


--  RANK.EQ find the ranking of a value in the column 

--  passed as second argument. The column values are sorted

--  according to the third argument (default DESC).

DEFINE

    TABLE SampleData1 = { 10, 20, 30, 40, 50, 60, 70, 80, 90, 100 }

    TABLE SampleData2 = { 20, 40, 60, 80, 100, 10, 30, 50, 70, 90 }

EVALUATE

{

    ( "RANK.EQ ASC 1", RANK.EQ ( 70, SampleData1[Value], ASC ) ),

    ( "RANK.EQ ASC 2", RANK.EQ ( 70, SampleData2[Value], ASC ) ),

    ( "RANK.EQ DESC 1", RANK.EQ ( 70, SampleData1[Value], DESC ) ),

    ( "RANK.EQ DESC 2", RANK.EQ ( 70, SampleData2[Value], DESC ) ),

    ( "RANK.EQ 1", RANK.EQ ( 70, SampleData1[Value] ) ),

    ( "RANK.EQ 2", RANK.EQ ( 70, SampleData2[Value] ) )

}

```

| Value1 | Value2 |
| --- | --- |
| RANK.EQ ASC 1 | 7 |
| RANK.EQ ASC 2 | 7 |
| RANK.EQ DESC 1 | 4 |
| RANK.EQ DESC 2 | 4 |
| RANK.EQ 1 | 4 |
| RANK.EQ 2 | 4 |

```dax


--  RANKX can be used instead of RANK.EQ, iterating

--  the table containing the column to analyze 

--  for the ranking

DEFINE

    TABLE SampleData = { 20, 40, 60, 80, 100, 10, 30, 50, 70, 90 }

EVALUATE

{

    ( "RANK.EQ", RANK.EQ ( 70, SampleData[Value] ) ),

    ( "RANKX", RANKX ( SampleData, SampleData[Value], 70 ) ),

    ( "RANK.EQ ASC", RANK.EQ ( 70, SampleData[Value], ASC ) ),

    ( "RANKX ASC", RANKX ( SampleData, SampleData[Value], 70, ASC ) )

}

```

| Value1 | Value2 |
| --- | --- |
| RANK.EQ | 4 |
| RANKX | 4 |
| RANK.EQ ASC | 7 |
| RANKX ASC | 7 |

```dax


--  RANK.EQ keeps duplicated value in the sorted column

--  to evaluate the ranking. The ranking obtained skips

--  the rank number in case of ties (SKIP argument of RANKX).

DEFINE

    TABLE SampleData = { "C", "B", "B", "A", "F", "D", "B" }

    -- Sorted: A, B, B, B, C, D, F

EVALUATE

{

    ( "RANK.EQ ASC", RANK.EQ ( "A", SampleData[Value], ASC ) ),

    ( "RANK.EQ ASC", RANK.EQ ( "B", SampleData[Value], ASC ) ),

    ( "RANK.EQ ASC", RANK.EQ ( "C", SampleData[Value], ASC ) ),

    ( "RANKX ASC", RANKX ( SampleData, SampleData[Value], "C", ASC, SKIP ) ),

    ( "RANK.EQ", RANK.EQ ( "A", SampleData[Value] ) ),

    ( "RANK.EQ", RANK.EQ ( "B", SampleData[Value] ) ),

    ( "RANK.EQ", RANK.EQ ( "C", SampleData[Value] ) ),

    ( "RANKX", RANKX ( SampleData, SampleData[Value], "C" ) )

}



```

| Value1 | Value2 |
| --- | --- |
| RANK.EQ ASC | 1 |
| RANK.EQ ASC | 2 |
| RANK.EQ ASC | 5 |
| RANKX ASC | 5 |
| RANK.EQ | 7 |
| RANK.EQ | 4 |
| RANK.EQ | 3 |
| RANKX | 3 |

## Related functions

Other related functions are:

- [[RANKX]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber, Jorge Esteves

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/rank-eq-function-dax](https://docs.microsoft.com/en-us/dax/rank-eq-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
