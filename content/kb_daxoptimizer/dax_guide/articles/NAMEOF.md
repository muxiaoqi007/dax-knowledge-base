---
title: "NAMEOF"
function: "nameof"
category: "Information"
url: "https://dax.guide/nameof/"
source: "dax.guide"
重要度:
难度:
---

# NAMEOF DAX Function (Information) Volatile

Returns the name of a table, column, measure, or calendar as a text string. Optional parameters control which component of the name is returned and how the result is escaped.

## Syntax

NAMEOF ( <Value> [, <Component>] [, <Escaped>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Value |  | Any fully-qualified column or measure or table or calendar reference. |
| Component | Optional | The `component` parameter accepts the following values:   | Value | Description | | --- | --- | | `TABLE` | Returns the table name. Returns an error if the object is not associated with a table (e.g., a calendar). | | `COLUMN` | Returns the column name. Returns an error if the object is not a column. | | `MEASURE` | Returns the measure name. Returns an error if the object is not a measure. | | `CALENDAR` | Returns the calendar name. Returns an error if the object is not a calendar. | | `FULL` | (Default) Returns the fully qualified name of the object. | | `SELF` | Returns the name of the object itself: the column or measure name for columns and measures, or the table/calendar name for tables and calendars. | | `PARENT` | Returns the parent table name for columns and measures. Returns an error for tables and calendars. | |
| Escaped | Optional | The `escaped` parameter accepts the following values:   | Value | Description | | --- | --- | | `ESCAPED` | (Default) Returns the name with full DAX escaping: table names wrapped in single quotes, column and measure names wrapped in square brackets. | | `UNESCAPED` | Returns the raw name without any delimiters or escape characters. Returns an error for fully qualified names that contain both a parent and child component. | | `MINIMALLYESCAPED` | Returns the name with escaping applied only when the name requires it. Names that contain only simple letters, digits, and underscores are returned without delimiters. Names that contain spaces or special characters are returned with escaping. | |

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

A text string with the requested name, formatted based on the component and escaped parameters.

## Remarks

- When called with only the `object` argument, NAMEOF behaves the same as in previous versions, returning a fully qualified, escaped name. Because `component` defaults to `FULL` and `escaped` defaults to `ESCAPED`, the return formats are:
  - For tables: `'TableName'`.
  - For columns: `'TableName'[ColumnName]`.
  - For measures: `'TableName'[MeasureName]`.
  - For calendars: `'CalendarName'`.
  - For variation columns: `'TableName'[ColumnName].[VariationName]`.
- Variables and dynamic expressions are not supported as arguments to NAMEOF.
- This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.

[» 3 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


EVALUATE

{

    NAMEOF ( Sales[Sales Amount] ),   // Measure reference

    NAMEOF ( [Sales Amount] ),        // Measure reference

    NAMEOF ( Customer[Birth Date] )   // Column reference

}



```

| Value |
| --- |
| ‘Sales'[Sales Amount] |
| ‘Sales'[Sales Amount] |
| ‘Customer'[Birth Date] |

## Related articles

Learn more about NAMEOF in the following articles:

- [**Using field parameters and calculation groups for conditional formatting**](https://www.sqlbi.com/articles/using-field-parameters-and-calculation-groups-for-conditional-formatting/)

  This article describes how to apply conditional formatting on measures picked from a slicer and implemented using two techniques: field parameters and calculation groups. [» Read more](https://www.sqlbi.com/articles/using-field-parameters-and-calculation-groups-for-conditional-formatting/)
- [**Fields parameters in Power BI**](https://www.sqlbi.com/articles/fields-parameters-in-power-bi/)

  This article analyzes the fields parameters feature in Power BI, unveiling some of the internals of its implementation. [» Read more](https://www.sqlbi.com/articles/fields-parameters-in-power-bi/)
- [**Understanding parameter types in DAX user-defined functions (UDF)**](https://www.sqlbi.com/articles/understanding-parameter-types-in-dax-user-defined-functions-udf/)

  This article describes the parameter types available in DAX user-defined functions, focusing on the specialized reference types MEASUREREF, COLUMNREF, TABLEREF, and CALENDARREF. [» Read more](https://www.sqlbi.com/articles/understanding-parameter-types-in-dax-user-defined-functions-udf/)

## Related functions

Other related functions are:

- [[TABLEOF]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://learn.microsoft.com/dax/nameof-function-dax](https://learn.microsoft.com/dax/nameof-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
