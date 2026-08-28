---
title: "PATH"
function: "path"
category: "Parent-child"
url: "https://dax.guide/path/"
source: "dax.guide"
重要度:
难度:
---

# PATH DAX Function (Parent-child)

Returns a string which contains a delimited list of IDs, starting with the top/root of a hierarchy and ending with the specified ID.

## Syntax

PATH ( <ID\_ColumnName>, <Parent\_ColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ID\_ColumnName |  | The column containing the IDs for each row. |
| Parent\_ColumnName |  | The column containing the parent IDs for each row. |

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

A delimited text string containing the identifiers of all the parents to the current identifier.

## Remarks

This function is used in tables that have some kind of internal hierarchy, to return the items that are related to the current row value. For example, in an Employees table that contains employees, the managers of employees, and the managers of the managers, you can return the path that connects an employee to his or her manager.

The path is not constrained to a single level of parent-child relationships; it can return related rows that are several levels up from the specified starting row.  
The two arguments must be column references, they cannot be expressions.

- The delimiter used to separate the ascendants is the vertical bar, ‘|’.
- The values in ID\_columnName and parent\_columnName must have the same data type, text or integer.
- Values in parent\_columnName must be present in ID\_columnName. That is, you cannot look up a parent if there is no value at the child level.
- If parent\_columnName is [[BLANK]] then PATH() returns ID\_columnName value. In other words, if you look for the manager of an employee but the parent\_columnName column has no data, the PATH function returns just the employee ID.
- If ID\_columnName has duplicates and parent\_columnName is the same for those duplicates then PATH() returns the common parent\_columnName value; however, if parent\_columnName value is different for those duplicates then PATH() returns an error. In other words, if you have two listings for the same employee ID and they have the same manager ID, the PATH function returns the ID for that manager. However, if there are two identical employee IDs that have different manager IDs, the PATH function returns an error.
- If ID\_columnName is [[BLANK]] then PATH() returns [[BLANK]].
- If ID\_columnName contains a vertical bar ‘|’ then PATH() returns an error.

[» 1 related article](#articles)  
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

Learn more about PATH in the following articles:

- [**Parent-Child Hierarchies**](https://www.daxpatterns.com/parent-child-hierarchies/)

  DAX does not directly support parent-child hierarchies. To obtain a browsable hierarchy in the data model, you have to naturalize a parent-child hierarchy. DAX provides specific functions to naturalize a parent-child hierarchy using calculated columns. The complete pattern also includes measures that improve the visualization of ragged hierarchies in Power Pivot. [» Read more](https://www.daxpatterns.com/parent-child-hierarchies/)

## Related functions

Other related functions are:

- [[PATHCONTAINS]]
- [[PATHITEM]]
- [[PATHITEMREVERSE]]
- [[PATHLENGTH]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/path-function-dax](https://docs.microsoft.com/en-us/dax/path-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
