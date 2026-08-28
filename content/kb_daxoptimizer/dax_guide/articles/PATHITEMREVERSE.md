---
title: "PATHITEMREVERSE"
function: "pathitemreverse"
category: "Parent-child"
url: "https://dax.guide/pathitemreverse/"
source: "dax.guide"
重要度:
难度:
---

# PATHITEMREVERSE DAX Function (Parent-child)

Returns the nth item in the delimited list produced by the Path function, counting backwards from the last item in the path.

## Syntax

PATHITEMREVERSE ( <Path>, <Position> [, <Type>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Path |  | A string which contains a delimited list of IDs. |
| Position |  | An integer denoting the position from the right end of the path. |
| Type | Optional | Optional. If missing or TEXT or 0 then this function returns a string. If INTEGER or 1 then this function returns an integer. |

## Return values

Scalar A single value of one these types: [integer](https://dax.guide/dt/integer), [string](https://dax.guide/dt/string).

The n-position ascendant in the given path, counting from current to the oldest.

## Remarks

- This function can be used to get an individual item from a hierarchy resulting from a [[PATH]] function.
- This function reverses the standard order of the hierarchy, so that closest items are listed first, For example, if the PATh function returns a list of managers above an employee in a hierarchy, the PATHITEMREVERSE function returns the employee’s immediate manager in position 2 because position 1 contains the employee’s id.
- If the number specified for position is less than one (1) or greater than the number of elements in path, the [[PATHITEM]] function returns [[BLANK]].
- If type is not a valid enumeration element an error is returned.

[» 4 related functions](#alt)  

## Examples

```dax


--  PATHITEM returns the nth item of a path column, starting to count

--  from the beginning. It returns BLANK if no such element exists.

--

--  PATHITEMREVERSE starts to count from the tail.

--

--  The table should have enough column for the number of levels in the hierarchy

--  otherwise certain levels are not visible. In this example, only 4 levels 

--  are visible.

DEFINE

    COLUMN Account[AccountPath] =

        PATH ( Account[AccountKey], Account[ParentAccountKey] )

    COLUMN Account[Level1] = PATHITEM ( Account[AccountPath], 1, INTEGER )

    COLUMN Account[Level2] = PATHITEM ( Account[AccountPath], 2, INTEGER )

    COLUMN Account[Level3] = PATHITEM ( Account[AccountPath], 3, INTEGER )

    COLUMN Account[Level4] = PATHITEM ( Account[AccountPath], 4, INTEGER )

    COLUMN Account[Level1R] = PATHITEMREVERSE ( Account[AccountPath], 1, INTEGER )

    COLUMN Account[Level2R] = PATHITEMREVERSE ( Account[AccountPath], 2, INTEGER )

    COLUMN Account[Level3R] = PATHITEMREVERSE ( Account[AccountPath], 3, INTEGER )

    COLUMN Account[Level4R] = PATHITEMREVERSE ( Account[AccountPath], 4, INTEGER )

EVALUATE

Account

```

| AccountKey | ParentAccountKey | AccountName | AccountPath | Level1 | Level2 | Level3 | Level4 | Level1R | Level2R | Level3R | Level4R |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | (Blank) | Profit and Loss after tax | 1 | 1 | (Blank) | (Blank) | (Blank) | 1 | (Blank) | (Blank) | (Blank) |
| 2 | 24 | Income | 1|24|2 | 1 | 24 | 2 | (Blank) | 2 | 24 | 1 | (Blank) |
| 3 | 24 | Expense | 1|24|3 | 1 | 24 | 3 | (Blank) | 3 | 24 | 1 | (Blank) |
| 4 | 2 | Sale Revenue | 1|24|2|4 | 1 | 24 | 2 | 4 | 4 | 2 | 24 | 1 |
| 5 | 3 | Cost of Goods Sold | 1|24|3|5 | 1 | 24 | 3 | 5 | 5 | 3 | 24 | 1 |
| 6 | 3 | Selling, General & Administrative Expenses | 1|24|3|6 | 1 | 24 | 3 | 6 | 6 | 3 | 24 | 1 |
| 7 | 6 | Administration Expense | 1|24|3|6|7 | 1 | 24 | 3 | 6 | 7 | 6 | 3 | 24 |
| 8 | 6 | IT Cost | 1|24|3|6|8 | 1 | 24 | 3 | 6 | 8 | 6 | 3 | 24 |
| 9 | 6 | Human Capital | 1|24|3|6|9 | 1 | 24 | 3 | 6 | 9 | 6 | 3 | 24 |
| 10 | 6 | Light, Heat, Communication Cost | 1|24|3|6|10 | 1 | 24 | 3 | 6 | 10 | 6 | 3 | 24 |
| 11 | 6 | Property Costs | 1|24|3|6|11 | 1 | 24 | 3 | 6 | 11 | 6 | 3 | 24 |
| 12 | 6 | Other Expenses | 1|24|3|6|12 | 1 | 24 | 3 | 6 | 12 | 6 | 3 | 24 |
| 13 | 6 | Marketing Cost | 1|24|3|6|13 | 1 | 24 | 3 | 6 | 13 | 6 | 3 | 24 |
| 14 | 13 | Holiday Ad Cost | 1|24|3|6|13|14 | 1 | 24 | 3 | 6 | 14 | 13 | 6 | 3 |
| 15 | 13 | Spring Ad Cost | 1|24|3|6|13|15 | 1 | 24 | 3 | 6 | 15 | 13 | 6 | 3 |
| 16 | 13 | Back-to-School Ad Cost | 1|24|3|6|13|16 | 1 | 24 | 3 | 6 | 16 | 13 | 6 | 3 |
| 17 | 13 | Business Ad Cost | 1|24|3|6|13|17 | 1 | 24 | 3 | 6 | 17 | 13 | 6 | 3 |
| 18 | 13 | Tax Time / Summer Ad Cost | 1|24|3|6|13|18 | 1 | 24 | 3 | 6 | 18 | 13 | 6 | 3 |
| 19 | 1 | Taxation | 1|19 | 1 | 19 | (Blank) | (Blank) | 19 | 1 | (Blank) | (Blank) |
| 20 | 14 | Radio & TV | 1|24|3|6|13|14|20 | 1 | 24 | 3 | 6 | 20 | 14 | 13 | 6 |
| 21 | 14 | Print | 1|24|3|6|13|14|21 | 1 | 24 | 3 | 6 | 21 | 14 | 13 | 6 |
| 22 | 14 | Internet | 1|24|3|6|13|14|22 | 1 | 24 | 3 | 6 | 22 | 14 | 13 | 6 |
| 23 | 14 | Other | 1|24|3|6|13|14|23 | 1 | 24 | 3 | 6 | 23 | 14 | 13 | 6 |
| 24 | 1 | Profit and Loss before tax | 1|24 | 1 | 24 | (Blank) | (Blank) | 24 | 1 | (Blank) | (Blank) |

## Related functions

Other related functions are:

- [[PATH]]
- [[PATHCONTAINS]]
- [[PATHITEM]]
- [[PATHLENGTH]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/pathitemreverse-function-dax](https://docs.microsoft.com/en-us/dax/pathitemreverse-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
