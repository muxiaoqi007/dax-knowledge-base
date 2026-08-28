---
title: "COUNTBLANK"
function: "countblank"
category: "Aggregation"
url: "https://dax.guide/countblank/"
source: "dax.guide"
重要度:
难度:
---

# COUNTBLANK DAX Function (Aggregation)

Counts the number of blanks in a column.

## Syntax

COUNTBLANK ( <ColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName |  | The column containing the blanks to be counted. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

If no rows are found that meet the blank condition, the function returns blank.

## Remarks

The function never returns 0. If there are no rows or no blanks, it returns always blank.

Any empty string is considered as a blank for COUNTBLANK purposes, even though [[ISBLANK]] would return FALSE for an empty string.

Even though COUNTBLANK is semantically equivalent to the following expressions, it may be slower than corresponding syntax based on [[CALCULATE]].

```dax


-- Example of COUNTBLANK over a column

COUNTBLANK ( 'Table'[Column] )



-- Semantically equivalent expression that can be faster (only for columns that are not strings):

CALCULATE (

    COUNTROWS ( 'Table' ),

    KEEPFILTERS ( ISBLANK ( 'Table'[Column] ) )

)



-- Semantically equivalent expression that can be faster (only for columns that are strings):

CALCULATE ( 

    COUNTROWS ( 'Table' ), 

    KEEPFILTERS ( 'Table'[Value] = "" ) 

)

```

[» 2 related functions](#alt)  

## Examples

```dax


--  COUNTBLANK counts both empty strings and blanks

--  For numeric columns, COUNTBLANK only considers blanks

DEFINE

    MEASURE Customer[# No Name 1] = COUNTBLANK ( Customer[Customer Name] )

    MEASURE Customer[# No Name 2] = 

        CALCULATE ( 

            COUNTROWS ( Customer ), 

            KEEPFILTERS ( Customer[Customer Name] = "" ) 

        )

    MEASURE Customer[# No Birth Date 1] =

        COUNTBLANK ( Customer[Birth Date] )

    MEASURE Customer[# No Birth Date 2] =

        CALCULATE ( 

            COUNTROWS ( Customer ), 

            KEEPFILTERS ( ISBLANK ( Customer[Birth Date] ) ) 

        )

EVALUATE

SUMMARIZECOLUMNS (

    Customer[Continent],

    "# No Name 1", [# No Name 1],

    "# No Name 2", [# No Name 2],

    "# No Birth Date 1", [# No Birth Date 1],

    "# No Birth Date 2", [# No Birth Date 2]

)

```

| Continent | # No Name 1 | # No Name 2 | # No Birth Date 1 | # No Birth Date 2 |
| --- | --- | --- | --- | --- |
| North America | 276 | 276 | 276 | 276 |
| Europe | 42 | 42 | 42 | 42 |
| Asia | 67 | 67 | 67 | 67 |

```dax


--  COUNTBLANK handles empty strings like blanks

--  The version with CALCULATE makes the intention of the developer clearer

DEFINE

    MEASURE Customer[# COUNTBLANK] =

        COUNTBLANK ( Customer[Customer Name] )

    MEASURE Customer[# EMPTY STRING / BLANK] =

        CALCULATE ( COUNTROWS ( Customer ), Customer[Customer Name] = "" )

    MEASURE Customer[# EMPTY STRING ONLY] =

        CALCULATE ( COUNTROWS ( Customer ), Customer[Customer Name] == "" )

    MEASURE Customer[# BLANK ONLY 1] =

        CALCULATE ( COUNTROWS ( Customer ), Customer[Customer Name] == BLANK () )

    MEASURE Customer[# BLANK ONLY 2] =

        CALCULATE ( COUNTROWS ( Customer ), ISBLANK ( Customer[Customer Name] ) )

EVALUATE

SUMMARIZECOLUMNS (

    Customer[Continent],

    "# COUNTBLANK", [# COUNTBLANK],

    "# EMPTY STRING / BLANK", [# EMPTY STRING / BLANK],

    "# EMPTY STRING ONLY", [# EMPTY STRING ONLY],

    "# BLANK ONLY 1", [# BLANK ONLY 1],

    "# BLANK ONLY 2", [# BLANK ONLY 2]

)

ORDER BY [Continent]

```

| Continent | # COUNTBLANK | # EMPTY STRING / BLANK | # EMPTY STRING ONLY | # BLANK ONLY 1 | # BLANK ONLY 2 |
| --- | --- | --- | --- | --- | --- |
| Asia | 67 | 67 | 33 | 34 | 34 |
| Europe | 42 | 42 | 21 | 21 | 21 |
| North America | 276 | 276 | 138 | 138 | 138 |

## Related functions

Other related functions are:

- [[COUNT]]
- [[COUNTROWS]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/countblank-function-dax](https://docs.microsoft.com/en-us/dax/countblank-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
