---
title: "AVERAGEA"
function: "averagea"
category: "Aggregation"
url: "https://dax.guide/averagea/"
source: "dax.guide"
重要度:
难度:
---

# AVERAGEA DAX Function (Aggregation) Not recommended

Returns the average (arithmetic mean) of the values in a column. Handles text and non-numeric values.

## Syntax

AVERAGEA ( <ColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName |  | A column that contains the values for which you want the average. |

## Return values

Scalar A single value of one these types: [currency](https://dax.guide/dt/currency), [decimal](https://dax.guide/dt/decimal).

## Remarks

The AVERAGEA function takes a column and averages the numbers in it.  
Whenever there are no rows to aggregate, the function returns a blank.

AVERAGEA manages a Boolean data type as an integer, where FALSE is 0 and TRUE is 1.  
AVERAGEA always consider a string as 0, regardless of the content of the string.

It is useless to use this function in DAX with a string column because the result is always 0, resulting in a different result compared to the corresponding AVERAGEA function in Excel. In order to calculate the average of the numbers included in a column with a string data type, use [[AVERAGEX]] instead of AVERAGEA converting the column into a number using [[VALUE]]:

For arguments that are not Boolean, the AVERAGEA function internally executes [[AVERAGEX]], without any performance difference.  
The following AVERAGEA call:

```dax


AVERAGEA ( table[column] )

```

corresponds to the following [[AVERAGEX]] call:

```dax


AVERAGEX (

    table,

    table[column] 

)

```

The result is blank in case there are no rows in the table with a non-blank value.

The behavior described above has an undocumented side effect caused by a bug that could be fixed in 2025 or later: if a measure is passed as an argument, it is evaluated by iterating the table where the measure is defined. We recommend not using this behavior, as it is misleading and breaks a calculation if the measure is moved to another table just to reorganize the presentation layer – it will also change once Microsoft fixes the bug.

[» 2 related functions](#alt)  

## Examples

```dax


-- The AVERAGEA syntax does not consider the content

-- of a string column (as Excel does)

AVERAGEA ( table[column] )



-- The following AVERAGEX syntax works correctly 

-- when table[column] is a string

AVERAGEX ( 

    table, 

    VALUE ( table[column] ) 

)

```

```dax


--  AVERAGE is the short version of AVERAGEX, when used with one column only

--  In DAX, there are no differences between AVERAGEA and AVERAGE

DEFINE

    MEASURE Sales[AVG Quantity 1] = AVERAGE ( Sales[Quantity] )

    MEASURE Sales[AVG Quantity 2] = AVERAGEX ( Sales, Sales[Quantity] )

    MEASURE Sales[AVG Line Amount] =

        AVERAGEX ( Sales, Sales[Quantity] * Sales[Net Price] )

EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Color],

    "AVG Quantity 1", [AVG Quantity 1],

    "AVG Quantity 2", [AVG Quantity 2],

    "AVG Line Amount", [AVG Line Amount]

)

```

| Color | AVG Quantity 1 | AVG Quantity 2 | AVG Line Amount |
| --- | --- | --- | --- |
| Silver | 1.40 | 1.40 | 344.49 |
| Blue | 1.41 | 1.41 | 388.00 |
| White | 1.40 | 1.40 | 266.75 |
| Red | 1.39 | 1.39 | 191.33 |
| Black | 1.40 | 1.40 | 243.68 |
| Green | 1.40 | 1.40 | 652.64 |
| Orange | 1.40 | 1.40 | 543.64 |
| Pink | 1.40 | 1.40 | 235.54 |
| Yellow | 1.42 | 1.42 | 47.90 |
| Purple | 1.36 | 1.36 | 79.65 |
| Brown | 1.40 | 1.40 | 559.52 |
| Grey | 1.40 | 1.40 | 411.63 |
| Gold | 1.41 | 1.41 | 365.89 |
| Azure | 1.37 | 1.37 | 244.70 |
| Silver Grey | 1.42 | 1.42 | 550.98 |
| Transparent | 1.40 | 1.40 | 3.68 |

More examples available for the AVERAGEX function.

## Related functions

Other related functions are:

- [[AVERAGE]]
- [[AVERAGEX]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Daniil Maslyuk

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/averagea-function-dax](https://docs.microsoft.com/en-us/dax/averagea-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
