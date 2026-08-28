---
title: "STARTOFWEEK"
function: "startofweek"
category: "Time Intelligence"
url: "https://dax.guide/startofweek/"
source: "dax.guide"
重要度:
难度:
---

# STARTOFWEEK DAX Function (Time Intelligence) Context Transition

Returns the start of week.

## Syntax

STARTOFWEEK ( <Calendar> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Calendar |  | The name of a calendar. |

## Return values

Table An entire table or a table with one or more columns.

A table containing the columns filtered.

## Notes

In order to use any time intelligence calculation, you need a well-formed date table. The *Date* table must satisfy the following requirements:

- All dates need to be present for the years required. The *Date* table must always start on January 1 and end on December 31, including all the days in this range. If the report only references fiscal years, then the date table must include all the dates from the first to the last day of a fiscal year. For example, if the fiscal year 2008 starts on July 1, 2007, then the *Date* table must include all the days from July 1, 2007 to June 30, 2008.
- There needs to be a column with a *DateTime* or *Date* data type containing unique values. This column is usually called *Date*. Even though the *Date* column is often used to define relationships with other tables, this is not required. Still, the *Date* column must contain unique values and should be referenced by the Mark as Date Table feature. In case the column also contains a time part, no time should be used – for example, the time should always be 12:00 am.
- The *Date* table must be marked as a date table in the model, in case the relationship between the *Date* table and any other table is not based on the *Date*.

The result of time intelligence functions has the same data lineage as the date column or table provided as an argument.

## Related functions

Other related functions are:

- [[ENDOFMONTH]]
- [[ENDOFQUARTER]]
- [[ENDOFWEEK]]
- [[ENDOFYEAR]]
- [[STARTOFMONTH]]
- [[STARTOFQUARTER]]
- [[STARTOFYEAR]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/startofweek-function-dax](https://learn.microsoft.com/en-us/dax/startofweek-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
