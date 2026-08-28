---
title: "COUNTROWS"
function: "countrows"
category: "Aggregation"
url: "https://dax.guide/countrows/"
source: "dax.guide"
重要度:
难度:
---

# COUNTROWS DAX Function (Aggregation)

Counts the number of rows in a table.

## Syntax

COUNTROWS ( [<Table>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table | Optional | The table containing the rows to be counted. If it is not specified, it uses the table containing the measure definition. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

Number of rows obtained by the evaluation of the table expression. If the table has no rows, it returns blank.

## Remarks

This function can be used to count the rows of a table expression.

Even though the table argument is optional, it is a best practice to always specify the first argument to improve readability and simplify code refactoring when needed.

[» 4 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

The following are valid syntaxes.

```dax


COUNTROWS ( table )

COUNTROWS ( DISTINCT ( table ) )

COUNTROWS ( VALUES ( table ) )

```

The COUNTROWS function can be used to count the unique values available in a column for the current filter context. However, DISTINCTCOUNT is better in that case. The following expressions are equivalent.

```dax


COUNTROWS ( DISTINCT ( table[column] ) )

DISTINCTCOUNT ( table[column] ) )

```

The COUNTROWS function can be used to check whether a column has only one item filtered/selected in the current filter context. However, HASONEVALUE is better in that case. The following expressions are equivalent.

```dax


COUNTROWS ( VALUES ( table[column] ) ) = 1

HASONEVALUE ( table[column] ) )

```

```dax


--  COUNTROWS counts the number of rows in a table

DEFINE

    MEASURE Customer[# Customers] = 

        COUNTROWS ( Customer )

    MEASURE Customer[# Countries 1] = 

        COUNTROWS ( DISTINCT ( Customer[CountryRegion] ) )

    MEASURE Customer[# Countries 2] = 

        DISTINCTCOUNT ( Customer[CountryRegion] )

EVALUATE

SUMMARIZECOLUMNS (

    Customer[Continent],

    "# Customers", [# Customers],

    "# Countries 1", [# Countries 1],

    "# Countries 2", [# Countries 2]

)

```

| Continent | # Customers | # Countries 1 | # Countries 2 |
| --- | --- | --- | --- |
| Asia | 3,658 | 15 | 15 |
| North America | 9,665 | 2 | 2 |
| Europe | 5,546 | 12 | 12 |

```dax


--  COUNTROWS is often used inside CALCULATE to count

--  the number of rows in a filtered table

DEFINE

    MEASURE Customer[# Individuals] =

        CALCULATE ( 

            COUNTROWS ( Customer ),

            Customer[Customer Type] = "Person"

        )

    MEASURE Customer[# Companies] =

        CALCULATE ( 

            COUNTROWS ( Customer ),

            Customer[Customer Type] = "Company"

        )

EVALUATE

SUMMARIZECOLUMNS (

    Customer[Continent],

    "# Individuals", [# Individuals],

    "# Companies", [# Companies]

)

```

| Continent | # Individuals | # Companies |
| --- | --- | --- |
| Asia | 3,591 | 67 |
| North America | 9,389 | 276 |
| Europe | 5,504 | 42 |

## Related articles

Learn more about COUNTROWS in the following articles:

- [**Handling BLANK in DAX**](https://www.sqlbi.com/articles/blank-handling-in-dax/)

  [» Read more](https://www.sqlbi.com/articles/blank-handling-in-dax/)
- [**Blank row in DAX**](https://www.sqlbi.com/articles/blank-row-in-dax/)

  There are two functions in DAX that return the list of values of a column: VALUES and DISTINCT. This article describes the difference between the two, explaining the details of the blank row added to tables for invalid relationships. [» Read more](https://www.sqlbi.com/articles/blank-row-in-dax/)
- [**Preparing a data model for Sankey Charts in Power BI**](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)

  This article describes how to correctly shape a data model and prepare data to use a Sankey Chart as a funnel, considering events related to a customer (contact, trial, subscription, renewal, and others). [» Read more](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)
- [**How to return 0 instead of BLANK in DAX**](https://www.sqlbi.com/articles/how-to-return-0-instead-of-blank-in-dax/)

  In this article we show various techniques to force a measure to return zero instead of blank, in order to highlight combinations of attributes with no data. [» Read more](https://www.sqlbi.com/articles/how-to-return-0-instead-of-blank-in-dax/)

## Related functions

Other related functions are:

- [[COUNT]]
- [[COUNTBLANK]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/countrows-function-dax](https://docs.microsoft.com/en-us/dax/countrows-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
