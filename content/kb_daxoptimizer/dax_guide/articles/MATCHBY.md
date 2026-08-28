---
title: "MATCHBY"
function: "matchby"
category: "Filter"
url: "https://dax.guide/matchby/"
source: "dax.guide"
重要度:
难度:
---

# MATCHBY DAX Function (Filter)

The columns used to determine how to match data and identify the current row. Can only be used within a Window function.

## Syntax

MATCHBY ( [<MatchBy\_ColumnName> [, <MatchBy\_ColumnName> [, … ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| MatchBy\_ColumnName | Optional Repeatable | A column used to determine how to identify the current row. |

## Return values

This function does not return a value.

## Related articles

Learn more about MATCHBY in the following articles:

- [**Preparing a data model for Sankey Charts in Power BI**](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)

  This article describes how to correctly shape a data model and prepare data to use a Sankey Chart as a funnel, considering events related to a customer (contact, trial, subscription, renewal, and others). [» Read more](https://www.sqlbi.com/articles/preparing-a-data-model-for-sankey-charts-in-power-bi/)
- [**Understanding apply semantics for window functions in DAX**](https://www.sqlbi.com/articles/understanding-apply-semantics-for-window-functions-in-dax/)

  This article explains the unique behavior of apply semantics: a new way of computing table expressions when multiple rows are selected in DAX window functions. [» Read more](https://www.sqlbi.com/articles/understanding-apply-semantics-for-window-functions-in-dax/)
- [**Account receivable aging in Power BI**](https://www.sqlbi.com/articles/account-receivable-aging-in-power-bi/)

  This article describes an Accounts Receivable Aging report in Power BI, and shows how to simplify a business problem using existing modeling patterns. [» Read more](https://www.sqlbi.com/articles/account-receivable-aging-in-power-bi/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/matchby-function-dax](https://learn.microsoft.com/en-us/dax/matchby-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
