---
title: "COUNTA"
function: "counta"
category: "Aggregation"
url: "https://dax.guide/counta/"
source: "dax.guide"
重要度:
难度:
---

# COUNTA DAX Function (Aggregation)

Counts the number of values in a column.

## Syntax

COUNTA ( <ColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName |  | The column that contains the values to be counted. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

Returns the number of cells in a column that contain a non blank value.

## Remarks

The only argument allowed to this function is a column.

When the function finds no rows that are non-blank, it returns a blank.

[[COUNT]] and COUNTA are identical in DAX for all the data types except Boolean. COUNTA can operate on a Boolean data type, whereas [[COUNT]] cannot do that.

The COUNTA function internally executes [[COUNTAX]], without any performance difference.  
The following COUNTA call:

```dax


COUNTA ( table[column] )

```

corresponds to the following [[COUNTAX]] call:

```dax


COUNTAX (

    table,

    table[column]

)

```

[» 1 related function](#alt)  

## Examples

```dax


--  COUNT is the short version of COUNTX, when used with one column only

--  In DAX, there are no differences between COUNTA and COUNT

--  COUNTX can be expressed in a more explicit way by using CALCULATE

--  and COUNTROWS

DEFINE

    MEASURE Customer[# Customers]     = COUNTROWS ( Customer )

    MEASURE Customer[# Individuals 1] = COUNT ( Customer[Customer Name] )

    MEASURE Customer[# Individuals 2] = COUNTX ( Customer, Customer[Customer Name] )

    MEASURE Customer[# Individuals 3] =

        CALCULATE ( 

            COUNTROWS ( Customer ), 

            NOT ISBLANK ( Customer[Customer Name] ) 

        )

EVALUATE

SUMMARIZECOLUMNS (

    Customer[Continent],

    "# Customers",     [# Customers],

    "# Individuals 1", [# Individuals 1],

    "# Individuals 2", [# Individuals 2],

    "# Individuals 3", [# Individuals 3]

)

```

| Continent | # Customers | # Individuals 1 | # Individuals 2 | # Individuals 3 |
| --- | --- | --- | --- | --- |
| Asia | 3,658 | 3,624 | 3,624 | 3,624 |
| North America | 9,665 | 9,527 | 9,527 | 9,527 |
| Europe | 5,546 | 5,525 | 5,525 | 5,525 |

```dax


--  COUNT does not count blanks, but it counts empty strings

--  using the CALCULATE version, the code is clearer

DEFINE

    MEASURE Customer[# COUNT] = COUNT ( Customer[Customer Name] )

    MEASURE Customer[# NO BLANKS] =

        CALCULATE ( 

            COUNTROWS ( Customer ), 

            NOT ISBLANK ( Customer[Customer Name] ) 

        )

    MEASURE Customer[# NO BLANKS / EMPTY STRINGS] =

        CALCULATE ( 

            COUNTROWS ( Customer ), 

            Customer[Customer Name] <> "" 

        )

EVALUATE

SUMMARIZECOLUMNS (

    Customer[Continent],

    "# COUNT", [# COUNT],

    "# NO BLANKS", [# NO BLANKS],

    "# NO BLANKS / EMPTY STRINGS", [# NO BLANKS / EMPTY STRINGS]

)



```

| Continent | # COUNT | # NO BLANKS | # NO BLANKS / EMPTY STRINGS |
| --- | --- | --- | --- |
| Asia | 3,624 | 3,624 | 3,591 |
| North America | 9,527 | 9,527 | 9,389 |
| Europe | 5,525 | 5,525 | 5,504 |

## Related functions

Other related functions are:

- [[COUNT]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/counta-function-dax](https://docs.microsoft.com/en-us/dax/counta-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
