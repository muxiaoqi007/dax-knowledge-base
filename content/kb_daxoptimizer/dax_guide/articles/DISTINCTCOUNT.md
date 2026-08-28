---
title: "DISTINCTCOUNT"
function: "distinctcount"
category: "Aggregation"
url: "https://dax.guide/distinctcount/"
source: "dax.guide"
重要度:
难度:
---

# DISTINCTCOUNT DAX Function (Aggregation)

Counts the number of distinct values in a column.

## Syntax

DISTINCTCOUNT ( <ColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName |  | The column for which the distinct values are counted. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

The number of distinct values in ColumnName.

## Remarks

The only argument allowed to this function is a column. You can use columns containing any type of data. When the function finds no rows to count, it returns a [[BLANK]], otherwise it returns the count of distinct values.

The syntax:

```dax


DISTINCTCOUNT ( table[column] )

```

corresponds to:

```dax


COUNTROWS ( DISTINCT ( table[column] ) )

```

[» 3 related articles](#articles)  
[» 4 related functions](#alt)  

## Examples

```dax


--  DISTINCTCOUNT counts the number of distinct values in a column

DEFINE

    MEASURE Customer[# Customers] = COUNTROWS ( Customer )        

    MEASURE Customer[# Names] =

        DISTINCTCOUNT ( Customer[Name] )

    MEASURE Customer[# Countries 1] =

        DISTINCTCOUNT ( Customer[CountryRegion] )

    MEASURE Customer[# Countries 2] =

        COUNTROWS ( DISTINCT ( Customer[CountryRegion] ) )

EVALUATE

SUMMARIZECOLUMNS (

    Customer[Continent],

    "# Customers", [# Customers],

    "# Names", [# Names],

    "# Countries 1", [# Countries 1],

    "# Countries 2", [# Countries 2]

)

```

| Continent | # Customers | # Names | # Countries 1 | # Countries 2 |
| --- | --- | --- | --- | --- |
| Asia | 3,658 | 3,583 | 15 | 15 |
| North America | 9,665 | 9,355 | 2 | 2 |
| Europe | 5,546 | 5,501 | 12 | 12 |

```dax


--

--  DISTINCTCOUNT considers BLANK as a valid value, whereas

--  DISTINCTCOUNTNOBLANK does not count any blank value

-- 

DEFINE

    MEASURE Customer[# Stores] =

        COUNTROWS ( Store )

    MEASURE Customer[# Manager] =

        DISTINCTCOUNT ( Store[Area Manager] )

    MEASURE Customer[# Manager (no blank)] =

        DISTINCTCOUNTNOBLANK ( Store[Area Manager] )

    MEASURE Customer[# Stores without manager] =

        COUNTBLANK ( Store[Area Manager] )

EVALUATE

SUMMARIZECOLUMNS ( 

    Store[Continent],

    "# Stores", [# Stores],

    "# Manager", [# Manager],

    "# Manager (no blank)", [# Manager (no blank)],

    "# Stores without manager", [# Stores without manager]

)

ORDER BY

    Store[Continent]

```

| Continent | # Stores | # Manager | # Manager (no blank) | # Stores without manager |
| --- | --- | --- | --- | --- |
| Asia | 41 | 5 | 5 | (Blank) |
| Europe | 54 | 8 | 7 | 7 |
| North America | 209 | 40 | 39 | 5 |

## Related articles

Learn more about DISTINCTCOUNT in the following articles:

- [**Related Distinct Count**](https://www.daxpatterns.com/related-distinct-count/)

  The Related Distinct Count pattern allows you to apply the distinct count calculation to any column in any table in the data model. Instead of just counting the number of distinct count values in the entire table using only the DISTINCTCOUNT function, the pattern filters only those values related to events filtered in another table. [» Read more](https://www.daxpatterns.com/related-distinct-count/)
- [**Analyzing the performance of DISTINCTCOUNT in DAX**](https://www.sqlbi.com/articles/analyzing-distinctcount-performance-in-dax/)

  This article describes how to analyze the performance of a DAX measure based on a DISTINCTCOUNT calculation and how to evaluate possible optimizations. [» Read more](https://www.sqlbi.com/articles/analyzing-distinctcount-performance-in-dax/)
- [**Why Power BI totals might seem inaccurate**](https://www.sqlbi.com/articles/why-power-bi-totals-might-seem-inaccurate/)

  A common question is why Power BI totals are inaccurate because they do not display the sum of individual rows. In this article, we explain the reasons why those totals are correct. [» Read more](https://www.sqlbi.com/articles/why-power-bi-totals-might-seem-inaccurate/)

## Related functions

Other related functions are:

- [[VALUES]]
- [[DISTINCT]]
- [[APPROXIMATEDISTINCTCOUNT]]
- [[DISTINCTCOUNTNOBLANK]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/distinctcount-function-dax](https://docs.microsoft.com/en-us/dax/distinctcount-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
