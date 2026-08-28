---
title: "ROWNUMBER"
function: "rownumber"
url: "https://dax.guide/rownumber/"
source: "dax.guide"
重要度:
难度:
---

# ROWNUMBER DAX Function

Returns the unique rank for the current context within the specified partition sorted by the specified order or on the axis specified.

## Syntax

ROWNUMBER ( [<Relation>] [, <OrderBy>] [, <Blanks>] [, <PartitionBy>] [, <MatchBy>] [, <Reset>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Relation | Optional | A table expression where the [[RANK]] is computed. If omitted, OrderBy must be explicitly specified. |
| OrderBy | Optional | Columns that define how each partition is sorted. If omitted, Relation must be explicitly specified. |
| Blanks | Optional | Defines how to handle [[BLANK]] OrderBy values. Valid values include: DEFAULT, [[FIRST]], [[LAST]]. |
| PartitionBy | Optional | Columns that define how Relation is partitioned. |
| MatchBy | Optional | Columns that define how the current row is identified. |
| Reset | Optional | Specifies how the calculation restarts. Valid values are: None, LowestParent, HighestParent, or an integer. |

## Related articles

Learn more about ROWNUMBER in the following articles:

- [**Computing accurate percentages with row-level security in Power BI**](https://www.sqlbi.com/articles/computing-accurate-percentages-with-row-level-security-in-power-bi/)

  This article shows how to compute ratios when row-level security hides some of the data. If the percentage also includes the hidden rows in the comparison, you should customize the data model and the measures involved to get the right result. [» Read more](https://www.sqlbi.com/articles/computing-accurate-percentages-with-row-level-security-in-power-bi/)
- [**SQLBI+ updates in May 2023**](https://www.sqlbi.com/blog/marco/2024/05/27/sqlbi-updates-in-may-2023/)

  In 2023, we released the first draft of the Window functions in DAX whitepaper as part of SQLBI+. Since then, we have released a few updates and are now glad to announce the availability of the related 3-hour video course… [» Read more](https://www.sqlbi.com/blog/marco/2024/05/27/sqlbi-updates-in-may-2023/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/dax/rownumber-function-dax](https://learn.microsoft.com/dax/rownumber-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
