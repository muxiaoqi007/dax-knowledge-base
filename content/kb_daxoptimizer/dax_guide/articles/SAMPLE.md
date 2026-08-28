---
title: "SAMPLE"
function: "sample"
category: "Statistical"
url: "https://dax.guide/sample/"
source: "dax.guide"
重要度:
难度:
---

# SAMPLE DAX Function (Statistical)

Returns a sample subset from a given table expression.

## Syntax

SAMPLE ( <Size>, <Table>, <OrderBy> [, [<Order>] [, <OrderBy> [, [<Order>] [, … ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Size |  | Number of rows in the sample to be returned. |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | A table expression from which the sample is generated. || OrderBy  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression | Repeatable | A scalar expression evaluated for each row of the table. |
| Order | Optional Repeatable | The order to be applied. 0/FALSE/DESC – descending; 1/TRUE/ASC – ascending. |

## Return values

Table An entire table or a table with one or more columns.

A table consisting of a sample of Size rows of table or an empty table if Size is 0 (zero) or less.

## Remarks

A table consisting of a sample of N rows of table or an empty table if Size is 0 (zero) or less.

One OrderBy argument is required. The returned sample will be stable and deterministic, returning the first row, the last row, and evenly distributed rows between them.

If Size is 0 (zero) or less then SAMPLE returns an empty table.

In order to avoid duplicate values in the sample, the table provided as the second argument should be grouped by the column used for sorting.

## Examples

```dax


--  SAMPLE returns a given number of rows from a table expression.

--

--  The rows are evenly chosen following the order provided

--  in the third and fourth arguments

DEFINE

    TABLE SampleData = { 2, 4, 4, 4, 5, 5, 7, 9 }

EVALUATE

SAMPLE ( 3, SampleData, [Value], ASC )



EVALUATE

SAMPLE ( 3, SampleData, [Value], DESC )



-- Because SampleData has 8 elements, the elements considered are in position 1, 5, 8

-- The second query returns 5 instead of 4 because the sort order is descending

-- SAMPLE is deterministic when used over the same table with the same argument

```

| Value |
| --- |
| 2 |
| 5 |
| 9 |

| Value |
| --- |
| 9 |
| 4 |
| 2 |

```dax


--  SAMPLE returns a given number of rows from a table expression.

--  SAMPLE can be used with string columns.

EVALUATE 

    VALUES ( 'Product'[Color] )

ORDER BY [Color] ASC



EVALUATE 

    SAMPLE ( 5, VALUES ( 'Product'[Color] ), 'Product'[Color], ASC )

ORDER BY [Color] ASC



```

| Color |
| --- |
| Azure |
| Black |
| Blue |
| Brown |
| Gold |
| Green |
| Grey |
| Orange |
| Pink |
| Purple |
| Red |
| Silver |
| Silver Grey |
| Transparent |
| White |
| Yellow |

| Color |
| --- |
| Azure |
| Gold |
| Pink |
| Silver |
| Yellow |

```dax


--  SAMPLE returns a given number of rows from a table expression.

--  SAMPLE can be used with tables.

--  The sort order can be defined by one or more columns.

EVALUATE 

    SAMPLE ( 5, 'Product', 'Product'[Color], ASC, 'Product'[Brand], ASC )

ORDER BY 'Product'[Color] ASC, 'Product'[Brand] ASC



EVALUATE 

    SAMPLE ( 5, 'Product', 'Product'[Brand], ASC, 'Product'[Color], ASC )

ORDER BY 'Product'[Brand] ASC, 'Product'[Color] ASC



-- The results are different because the sort order is by Color and then Brand 

-- in the first query, and by Brand and Color in the second query

```

| ProductKey | Product Code | Product Name | Manufacturer | Brand | Subcategory | Category | Color | List Price |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1030 | 0401087 | A. Datum Slim Digital Camera M180 Azure | A. Datum Corporation | A. Datum | Digital Cameras | Cameras and camcorders | Azure | 148.00 |
| 2224 | 0806035 | Adventure Works Chandelier M8150 Blue | Adventure Works | Adventure Works | Lamps | Home Appliances | Blue | 268.50 |
| 1598 | 0602028 | SV DVD External DVD Burner M200 Grey | Southridge Video | Southridge Video | Movie DVD | Music, Movies and Audio Books | Grey | 57.88 |
| 1739 | 0702033 | MGS RalliSport Challenge E115 | Tailspin Toys | Tailspin Toys | Download Games | Games and Toys | Silver | 28.00 |
| 108 | 0106043 | WWI Stereo Bluetooth Headphones New Generation M370 Yellow | Wide World Importers | Wide World Importers | Bluetooth Headphones | Audio | Yellow | 132.99 |

| ProductKey | Product Code | Product Name | Manufacturer | Brand | Subcategory | Category | Color | List Price |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1037 | 0401094 | A. Datum Advanced Digital Camera M300 Azure | A. Datum Corporation | A. Datum | Digital Cameras | Cameras and camcorders | Azure | 188.50 |
| 821 | 0308079 | Contoso USB 2.0 Dock Station docking station M800 Grey | Contoso, Ltd | Contoso | Computers Accessories | Computers | Grey | 29.90 |
| 1169 | 0405026 | Fabrikam Home and Vacation Moviemaker 1/2” 3mm M300 White | Fabrikam, Inc. | Fabrikam | Camcorders | Cameras and camcorders | White | 566.00 |
| 936 | 0308194 | SV 4GB Laptop Memory M65 Black | Southridge Video | Southridge Video | Computers Accessories | Computers | Black | 79.00 |
| 108 | 0106043 | WWI Stereo Bluetooth Headphones New Generation M370 Yellow | Wide World Importers | Wide World Importers | Bluetooth Headphones | Audio | Yellow | 132.99 |

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/sample-function-dax](https://docs.microsoft.com/en-us/dax/sample-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
