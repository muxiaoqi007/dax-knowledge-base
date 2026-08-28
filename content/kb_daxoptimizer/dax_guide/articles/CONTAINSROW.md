---
title: "CONTAINSROW"
function: "containsrow"
category: "Information"
url: "https://dax.guide/containsrow/"
source: "dax.guide"
重要度:
难度:
---

# CONTAINSROW DAX Function (Information)

Returns TRUE if there exists at least one row where all columns have specified values.

## Syntax

CONTAINSROW ( <Table>, <Value> [, <Value> [, … ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table |  | The table to test. |
| Value | Repeatable | A scalar expression to look for in the corresponding column. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

A value of TRUE if a row of values exists in a table; otherwise, the function returns FALSE.

## Remarks

The [IN](https://dax.guide/op/in/) operator internally executes CONTAINSROW.

The number of scalarExprN must match the number of columns in tableExpr.

Unlike the = operator, the [IN](https://dax.guide/op/in/) operator and the CONTAINSROW function perform strict comparison. For example, the [[BLANK]] value does not match 0.

NOT IN is not an operator in DAX. To perform the logical negation of the IN operator, put NOT in front of the entire expression. For example:

```dax


NOT [Color] IN { "Red", "Yellow", "Blue" }

```

The following expressions are equivalent:

```dax


Product[Color] IN { "Red", "Blue", "Yellow" }

CONTAINSROW ( { "Red", "Blue", "Yellow" }, Product[Color] )

```

The following expressions using two columns are equivalent:

```dax


( 'Date'[Year], 'Date'[MonthNumber] ) IN { ( 2018, 12 ), ( 2019, 1 ) }

CONTAINSROW ( { ( 2018, 12 ), ( 2019, 1 ) }, 'Date'[Year], 'Date'[MonthNumber] )

```

[» 2 related articles](#articles)  

## Examples

```dax


--  CONTAINSROW requires to specify one value  

--  for each column of the lookup table.

DEFINE MEASURE Sales[Customers without stores] = 

    COUNTROWS (

        FILTER (

            Customer,

            NOT CONTAINSROW (

                SUMMARIZE ( Store, Store[CountryRegion], Store[City] ),

                Customer[CountryRegion],

                Customer[City]

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

```dax


--  Internally, the IN operator invokes a corresponding

--  CONTAINSROW filter.

--  The CONTAINSROW function has as many arguments after the

--  first one as the number of column in each row of the table

--  passed in the first argument. Only IN uses the tuples syntax

EVALUATE

{

     ( 1, "IN", COUNTROWS (

        FILTER (

            Customer,

             ( Customer[City], Customer[State] )

                IN { ( "New York", "New York" ), ( "Columbus", "Ohio" ) }

        )

    ) ),

     ( 2, "CONTAINSROW", COUNTROWS (

        FILTER (

            Customer,

            CONTAINSROW (

                { ( "New York", "New York" ), ( "Columbus", "Ohio" ) },

                Customer[City],

                Customer[State]

            )

        )

    ) )

}

```

| Value1 | Value2 | Value3 |
| --- | --- | --- |
| 1 | IN | 2 |
| 2 | CONTAINSROW | 2 |

## Related articles

Learn more about CONTAINSROW in the following articles:

- [**The IN operator in DAX**](https://www.sqlbi.com/articles/the-in-operator-in-dax/)

  This article describes the IN operator in DAX, which simplifies logical conditions checking whether a certain value is included in a list of values or expressions. [» Read more](https://www.sqlbi.com/articles/the-in-operator-in-dax/)
- [**Understanding the IN operator in DAX**](https://www.sqlbi.com/articles/understanding-the-in-operator-in-dax/)

  This article describes the IN operator in DAX, which simplifies logical conditions checking whether a certain value belongs to a list of values. [» Read more](https://www.sqlbi.com/articles/understanding-the-in-operator-in-dax/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/containsrow-function-dax](https://docs.microsoft.com/en-us/dax/containsrow-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
