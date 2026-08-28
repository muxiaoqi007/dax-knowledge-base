---
title: "CONTAINS"
function: "contains"
category: "Information"
url: "https://dax.guide/contains/"
source: "dax.guide"
重要度:
难度:
---

# CONTAINS DAX Function (Information)

Returns TRUE if there exists at least one row where all columns have specified values.

## Syntax

CONTAINS ( <Table>, <ColumnName>, <Value> [, <ColumnName>, <Value> [, … ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table |  | The table to test. |
| ColumnName | Repeatable | A column in the input table or in a related table. |
| Value | Repeatable | A scalar expression to look for in the column. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

A value of TRUE if each specified value can be found in the corresponding *columnName*, or are contained, in those columns; otherwise, the function returns FALSE.

## Remarks

- The arguments *columnName* and value must come in pairs; otherwise an error is returned.
- *columnName* must belong to the specified table, or to a table that is related to table.
- If *columnName* refers to a column in a related table then it must be fully qualified; otherwise, an error is returned.

Using CONTAINS is more efficient than using a similar syntax using [[COUNTROWS]] and [[FILTER]]:

```dax


COUNTROWS ( 

    FILTER (

        table,

        columnName = value

    )

) > 0

```

A common pattern with CONTAINS is that used for a virtual relationship, which is better implemented using [[TREATAS]] or [[INTERSECT]]. The following code:

```dax


CALCULATE ( 

    [measure],

    FILTER ( 

        ALL ( targetTable[targetColumn] ),

        CONTAINS ( 

            VALUES ( sourceTable[sourceColumn] ),

            sourceTable[sourceColumn],

            targetTable[targetColumn]

        )

    )

)

```

can be better written using [[TREATAS]]:

```dax


CALCULATE ( 

    [measure],

    TREATAS ( 

        VALUES ( sourceTable[sourceColumn] ),

        targetTable[targetColumn]

    )

)

```

If [[TREATAS]] is not available, [[INTERSECT]] provides a second choice alternative:

```dax


CALCULATE ( 

    [measure],

    INTERSECT ( 

        ALL (  targetTable[targetColumn] ),

        VALUES ( sourceTable[sourceColumn] )

    )

)

```

[» 5 related articles](#articles)  

## Examples

```dax


--  CONTAINS is useful to search in a table for the presence

--  of at least one row with a given set of values

DEFINE MEASURE Sales[Customers without stores] = 

    COUNTROWS (

        FILTER (

            Customer,

            NOT CONTAINS (

                Store,

                Store[CountryRegion], Customer[CountryRegion],

                Store[City],          Customer[City]

            )

        )

    )

EVALUATE

SUMMARIZECOLUMNS (

    Customer[CountryRegion],

    "Customers", COUNTROWS ( Customer ),

    "Customers without stores", [Customers without stores]

)

```

| CountryRegion | Customers | Customers without stores |
| --- | --- | --- |
| Australia | 3,631 | 3,524 |
| United States | 8,086 | 7,362 |
| Canada | 1,579 | 1,381 |
| Germany | 1,791 | 1,668 |
| United Kingdom | 1,931 | 1,271 |
| France | 1,814 | 1,426 |
| the Netherlands | 1 | (Blank) |
| Greece | 1 | (Blank) |
| Switzerland | 1 | (Blank) |
| Ireland | 1 | (Blank) |
| Portugal | 1 | (Blank) |
| Spain | 1 | (Blank) |
| Italy | 1 | (Blank) |
| Russia | 2 | (Blank) |
| Poland | 1 | (Blank) |
| Turkmenistan | 1 | (Blank) |
| Thailand | 1 | (Blank) |
| China | 5 | (Blank) |
| Kyrgyzstan | 1 | (Blank) |
| South Korea | 2 | (Blank) |
| Syria | 1 | (Blank) |
| Pakistan | 1 | (Blank) |
| India | 3 | (Blank) |
| Japan | 7 | 1 |
| Singapore | 1 | (Blank) |
| Taiwan | 1 | (Blank) |
| Iran | 1 | (Blank) |
| Bhutan | 1 | (Blank) |
| Armenia | 1 | (Blank) |

## Related articles

Learn more about CONTAINS in the following articles:

- [**Physical and Virtual Relationships in DAX**](https://www.sqlbi.com/articles/physical-and-virtual-relationships-in-dax/)

  [» Read more](https://www.sqlbi.com/articles/physical-and-virtual-relationships-in-dax/)
- [**Propagating filters using TREATAS in DAX**](https://www.sqlbi.com/articles/propagate-filters-using-treatas-in-dax/)

  [» Read more](https://www.sqlbi.com/articles/propagate-filters-using-treatas-in-dax/)
- [**The IN operator in DAX**](https://www.sqlbi.com/articles/the-in-operator-in-dax/)

  This article describes the IN operator in DAX, which simplifies logical conditions checking whether a certain value is included in a list of values or expressions. [» Read more](https://www.sqlbi.com/articles/the-in-operator-in-dax/)
- [**Understanding the IN operator in DAX**](https://www.sqlbi.com/articles/understanding-the-in-operator-in-dax/)

  This article describes the IN operator in DAX, which simplifies logical conditions checking whether a certain value belongs to a list of values. [» Read more](https://www.sqlbi.com/articles/understanding-the-in-operator-in-dax/)
- [**Using CONTAINS in DAX**](https://www.sqlbi.com/articles/using-contains-in-dax/)

  This article explains how the CONTAINS function works and what can be used as better alternatives in DAX in common use cases. [» Read more](https://www.sqlbi.com/articles/using-contains-in-dax/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/contains-function-dax](https://docs.microsoft.com/en-us/dax/contains-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
