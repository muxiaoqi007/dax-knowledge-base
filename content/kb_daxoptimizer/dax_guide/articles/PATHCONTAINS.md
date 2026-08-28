---
title: "PATHCONTAINS"
function: "pathcontains"
category: "Parent-child"
url: "https://dax.guide/pathcontains/"
source: "dax.guide"
重要度:
难度:
---

# PATHCONTAINS DAX Function (Parent-child)

Returns TRUE if the specified Item exists within the specified Path.

## Syntax

PATHCONTAINS ( <Path>, <Item> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Path |  | A string which contains a delimited list of IDs. |
| Item |  | A value to be found in the path. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

A value of TRUE if item exists in Path; otherwise FALSE.

## Remarks

If Item is an integer number it is converted to text and then the function is evaluated. If conversion fails then the function returns an error.

[» 3 related articles](#articles)  
[» 4 related functions](#alt)  

## Examples

```dax


--  PATH generates a column containing the path of each node in a 

--  parent/child hierarchy

--

--  PATHLENGTH returns the length of a path column

--  PATHCONTAINS checks if a path column contains a value

DEFINE

    COLUMN Account[AccountPath] = PATH ( Account[AccountKey], Account[ParentAccountKey] )

    COLUMN Account[PathLength] = PATHLENGTH ( Account[AccountPath] )

    COLUMN Account[PathContains2] = PATHCONTAINS ( Account[AccountPath], 2 )

EVALUATE

    Account

ORDER BY [AccountPath]

```

| AccountKey | ParentAccountKey | AccountName | AccountPath | PathLength | PathContains2 |
| --- | --- | --- | --- | --- | --- |
| 1 | (Blank) | Profit and Loss after tax | 1 | 1 | false |
| 19 | 1 | Taxation | 1|19 | 2 | false |
| 24 | 1 | Profit and Loss before tax | 1|24 | 2 | false |
| 2 | 24 | Income | 1|24|2 | 3 | true |
| 4 | 2 | Sale Revenue | 1|24|2|4 | 4 | true |
| 3 | 24 | Expense | 1|24|3 | 3 | false |
| 5 | 3 | Cost of Goods Sold | 1|24|3|5 | 4 | false |
| 6 | 3 | Selling, General & Administrative Expenses | 1|24|3|6 | 4 | false |
| 10 | 6 | Light, Heat, Communication Cost | 1|24|3|6|10 | 5 | false |
| 11 | 6 | Property Costs | 1|24|3|6|11 | 5 | false |
| 12 | 6 | Other Expenses | 1|24|3|6|12 | 5 | false |
| 13 | 6 | Marketing Cost | 1|24|3|6|13 | 5 | false |
| 14 | 13 | Holiday Ad Cost | 1|24|3|6|13|14 | 6 | false |
| 20 | 14 | Radio & TV | 1|24|3|6|13|14|20 | 7 | false |
| 21 | 14 | Print | 1|24|3|6|13|14|21 | 7 | false |
| 22 | 14 | Internet | 1|24|3|6|13|14|22 | 7 | false |
| 23 | 14 | Other | 1|24|3|6|13|14|23 | 7 | false |
| 15 | 13 | Spring Ad Cost | 1|24|3|6|13|15 | 6 | false |
| 16 | 13 | Back-to-School Ad Cost | 1|24|3|6|13|16 | 6 | false |
| 17 | 13 | Business Ad Cost | 1|24|3|6|13|17 | 6 | false |
| 18 | 13 | Tax Time / Summer Ad Cost | 1|24|3|6|13|18 | 6 | false |
| 7 | 6 | Administration Expense | 1|24|3|6|7 | 5 | false |
| 8 | 6 | IT Cost | 1|24|3|6|8 | 5 | false |
| 9 | 6 | Human Capital | 1|24|3|6|9 | 5 | false |

## Related articles

Learn more about PATHCONTAINS in the following articles:

- [**Parent-Child Hierarchies**](https://www.daxpatterns.com/parent-child-hierarchies/)

  DAX does not directly support parent-child hierarchies. To obtain a browsable hierarchy in the data model, you have to naturalize a parent-child hierarchy. DAX provides specific functions to naturalize a parent-child hierarchy using calculated columns. The complete pattern also includes measures that improve the visualization of ragged hierarchies in Power Pivot. [» Read more](https://www.daxpatterns.com/parent-child-hierarchies/)
- [**Managing hierarchical organizations in Power BI security roles**](https://www.sqlbi.com/articles/managing-hierarchical-organizations-in-power-bi-security-roles/)

  This article describes how to apply dynamic security roles in a hierarchical organization to minimize the maintenance effort on the security configuration and obtain the best performance at query time. [» Read more](https://www.sqlbi.com/articles/managing-hierarchical-organizations-in-power-bi-security-roles/)
- [**Strings list to table in DAX**](https://www.sqlbi.com/blog/marco/2024/05/18/strings-list-to-table-in-dax/)

  DAX is not like M when it comes to data manipulation, and it is not supposed to do that. However, if you need something in DAX similar to Table.FromList in M, this blog post is for you. If you have a list of values in a string in DAX and you want to obtain a table with one row for each item in the list, you can do the following: Use “|” as item separator in the string (instead of “,” or “;”) Determines the number of items in the string by using PATHLENGTH Iterates the string by using GENERATESERIES… [» Read more](https://www.sqlbi.com/blog/marco/2024/05/18/strings-list-to-table-in-dax/)

## Related functions

Other related functions are:

- [[PATH]]
- [[PATHITEM]]
- [[PATHITEMREVERSE]]
- [[PATHLENGTH]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/pathcontains-function-dax](https://docs.microsoft.com/en-us/dax/pathcontains-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
