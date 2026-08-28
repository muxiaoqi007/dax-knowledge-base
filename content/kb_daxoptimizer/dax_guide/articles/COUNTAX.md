---
title: "COUNTAX"
function: "countax"
category: "Aggregation"
url: "https://dax.guide/countax/"
source: "dax.guide"
重要度:
难度:
---

# COUNTAX DAX Function (Aggregation)

Counts the number of values which result from evaluating an expression for each row of a table.

## Syntax

COUNTAX ( <Table>, <Expression> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | The table containing the rows for which the expression will be evaluated. || Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | The expression to be evaluated for each row of the table. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

Returns the number of values that are non blank by iterating the provided table.

## Remarks

When the function finds no rows producing a non-blank value, it returns a blank.

[[COUNTX]] and COUNTAX are identical in DAX for all the data types except Boolean. COUNTAX can operate on a Boolean data type, whereas [[COUNTX]] cannot do that.

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


--  COUNTX is needed when you need to count the result of a formula

--  In this case, COUNTX reads better than CALCULATE

DEFINE

    MEASURE Customer[# Individuals Children/Car 1] =

        COUNTX ( Customer, DIVIDE ( Customer[Total Children], Customer[Cars Owned] ) )

    MEASURE Customer[# Individuals Children/Car 2] =

        COUNTROWS (

            FILTER (

                Customer,

                NOT ISBLANK ( DIVIDE ( Customer[Total Children], Customer[Cars Owned] ) )

            )

        )

EVALUATE

SUMMARIZECOLUMNS (

    Customer[Continent],

    "# Individuals Children/Car 1", [# Individuals Children/Car 1],

    "# Individuals Children/Car 2", [# Individuals Children/Car 2]

)

```

| Continent | # Individuals Children/Car 1 | # Individuals Children/Car 2 |
| --- | --- | --- |
| Asia | 3,203 | 3,203 |
| North America | 7,507 | 7,507 |
| Europe | 3,536 | 3,536 |

## Related functions

Other related functions are:

- [[COUNTX]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/countax-function-dax](https://docs.microsoft.com/en-us/dax/countax-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
