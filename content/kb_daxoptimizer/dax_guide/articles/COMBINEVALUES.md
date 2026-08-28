---
title: "COMBINEVALUES"
function: "combinevalues"
category: "Text"
url: "https://dax.guide/combinevalues/"
source: "dax.guide"
重要度:
难度:
---

# COMBINEVALUES DAX Function (Text)

Combines the given set of operands using a specified delimiter.

## Syntax

COMBINEVALUES ( <Delimiter>, <Expression1>, <Expression2> [, <Expression2> [, … ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Delimiter |  | Delimiter which is used to join the expressions into a single string. |
| Expression1 |  | First Expression to be evaluated and joined with other strings using the Separator. |
| Expression2 | Repeatable | Second Expression to be evaluated and joined with other strings using the Separator. |

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

The concatenated string.

## Remarks

The COMBINEVALUES function assumes, but does not validate, that when the input values are different, the output strings are also different. Based on this assumption, when COMBINEVALUES is used to create calculated columns in order to build a relationship that joins multiple columns from two DirectQuery tables, an optimized join condition is generated at query time.

For example, if users want to create a relationship between Table1(Column1, Column2) and Table2(Column1, Column2), they can create two calculated columns, one on each table, as:

```dax


Table1[CalcColumn] = COMBINEVALUES ( ",", Table1[Column1], Table1[Column2] )

```

and

```dax


Table2[CalcColumn] = COMBINEVALUES ( ",", Table2[Column1], Table2[Column2] )

```

And then create a relationship between Table1[CalcColumn] and Table2[CalcColumn]. Unlike other DAX functions and operators, which are translated literally to the corresponding SQL operators and functions, the above relationship generates a SQL join predicate as:

```dax


(Table1.Column1 = Table2.Column1 OR Table1.Column1 IS NULL AND Table2.Column1 IS NULL)

```

and

```dax


(Table1.Column2 = Table2.Column2 OR Table1.Column2 IS NULL AND Table2.Column2 IS NULL).

```

The join predicate can potentially deliver much better query performance than one that involves complex SQL operators and functions.

The COMBINEVALUES function relies on users to choose the appropriate delimiter to ensure that unique combinations of input values produce distinct output strings but it does not validate that the assumption is true. For example, if users choose "|" as the delimiter, but one row in Table1 has Table1[Column1] = "|" and Table1[Column2] = " ", while one row in Table2 has Table2[Column1] = "" and Table2[Column2] = "| ", the two concatenated outputs will be the same "|| ", which seem to indicate that the two rows are a match in the join operation. The two rows are not joined together if both tables are from the same DirectQuery source although they are joined together if both tables are imported.

[» 2 related articles](#articles)  

## Examples

```dax


--  COMBINEVALUES concatenates columns using a separator. 

--  The result is a simple string concatenation.

--  Its usage is in DirectQuery over SQL models. The DAX engine can 

--  generate optimized JOIN conditions for calculated columns created 

--  with COMBINEVALUES, in contrast with simple string concatenation.

EVALUATE

ADDCOLUMNS (

    SUMMARIZE (

        TOPN ( 5, Customer ),

        Customer[Customer Code],

        Customer[Customer Name],

        Customer[CountryRegion]

    ),

    "Name, Code, and Country combined",

        COMBINEVALUES (

            " | ",

            Customer[Customer Name],

            Customer[Customer Code],

            Customer[CountryRegion]

        )

)

```

| Customer Code | Customer Name | CountryRegion | Name, Code, and Country combined |
| --- | --- | --- | --- |
| 11024 | Xie, Russell | United States | Xie, Russell | 11024 | United States |
| 11036 | Russell, Jennifer | United States | Russell, Jennifer | 11036 | United States |
| 11041 | Carter, Amanda | United States | Carter, Amanda | 11041 | United States |
| 11043 | Simmons, Nathan | United States | Simmons, Nathan | 11043 | United States |
| 11928 | Morris, Isabella | United States | Morris, Isabella | 11928 | United States |

## Related articles

Learn more about COMBINEVALUES in the following articles:

- [**Using COMBINEVALUES to optimize DirectQuery performance**](https://www.sqlbi.com/articles/using-combinevalues-to-optimize-directquery-performance/)

  This article describes the behavior of the COMBINEVALUES function in DAX, and how it can optimize the performance of DirectQuery with multi-column relationships. [» Read more](https://www.sqlbi.com/articles/using-combinevalues-to-optimize-directquery-performance/)
- [**Creating a slicer that filters multiple columns in Power BI**](https://www.sqlbi.com/articles/creating-a-slicer-that-filters-multiple-columns-in-power-bi/)

  This article describes how to create a slicer showing the values of multiple columns, applying the filter on any of the underlying columns. [» Read more](https://www.sqlbi.com/articles/creating-a-slicer-that-filters-multiple-columns-in-power-bi/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Srivatshan, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/combinevalues-function-dax](https://docs.microsoft.com/en-us/dax/combinevalues-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
