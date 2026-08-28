---
title: "OPENINGBALANCEYEAR"
function: "openingbalanceyear"
category: "Time Intelligence"
url: "https://dax.guide/openingbalanceyear/"
source: "dax.guide"
重要度:
难度:
---

# OPENINGBALANCEYEAR DAX Function (Time Intelligence) Context Transition

Evaluates the specified expression for the date corresponding to the end of the previous year after applying specified filters.

## Syntax

OPENINGBALANCEYEAR ( <Expression>, <Dates> [, <Filter>] [, <YearEndDate>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Expression  By Expression |  | The expression to be evaluated. |
| Dates |  | The name of a column containing dates, a single column table containing dates, or a calendar reference. |
| Filter | Optional | A boolean (True/False) expression or a table expression that defines a filter. |
| YearEndDate | Optional | End of year date. |

## Return values

Scalar A single value of any type.

A scalar value that represents the expression evaluated at the first date of the year in the current context.

## Notes

In order to use any time intelligence calculation, you need a well-formed date table. The *Date* table must satisfy the following requirements:

- All dates need to be present for the years required. The *Date* table must always start on January 1 and end on December 31, including all the days in this range. If the report only references fiscal years, then the date table must include all the dates from the first to the last day of a fiscal year. For example, if the fiscal year 2008 starts on July 1, 2007, then the *Date* table must include all the days from July 1, 2007 to June 30, 2008.
- There needs to be a column with a *DateTime* or *Date* data type containing unique values. This column is usually called *Date*. Even though the *Date* column is often used to define relationships with other tables, this is not required. Still, the *Date* column must contain unique values and should be referenced by the Mark as Date Table feature. In case the column also contains a time part, no time should be used – for example, the time should always be 12:00 am.
- The *Date* table must be marked as a date table in the model, in case the relationship between the *Date* table and any other table is not based on the *Date*.

The result of time intelligence functions has the same data lineage as the date column or table provided as an argument.

## Remarks

The dates argument can be any of the following:

- A reference to a date/time column. Only in this case a context transition applies because the column reference is replaced by
  - [[CALCULATETABLE]] ( [[DISTINCT]] ( <Dates> ) )
- A table expression that returns a single column of date/time values.
- A Boolean expression that defines a single-column table of date/time values.

The result table includes only a date that exists in the dates column.

The syntax:

```dax


OPENINGBALANCEYEAR ( <Expression>, <Dates> [, <Filter>] [, <YearEndDate>] )

```

corresponds to:

```dax


CALCULATE ( 

    <Expression>,

    PREVIOUSDAY ( STARTOFYEAR ( <Dates> [, <YearEndDate>] ) )

    [, <Filter>]

)

```

[» 1 related article](#articles)  
[» 5 related functions](#alt)  

## Examples

```dax


DEFINE

    MEASURE Sales[Sales YTD] =

        CALCULATE ( [Sales Amount], DATESYTD ( 'Date'[Date] ) )

    MEASURE Sales[Sales OBQ] =

        OPENINGBALANCEYEAR ( [Sales YTD], 'Date'[Date] )

    MEASURE Sales[Sales CBQ] =

        CLOSINGBALANCEYEAR ( [Sales YTD], 'Date'[Date] )

EVALUATE

CALCULATETABLE (

    SUMMARIZECOLUMNS (

        'Date'[Calendar Year Quarter],

        'Date'[Calendar Year Quarter Number],

        "Sales", [Sales Amount],

        "Sales YTD", [Sales YTD],

        "Sales OBQ", [Sales OBQ],

        "Sales CBQ", [Sales CBQ]

    ),

    'Date'[Calendar Year Month Number] <= 200812

)

ORDER BY 'Date'[Calendar Year Quarter Number]

```

| Calendar Year Quarter | Calendar Year Quarter Number | Sales | Sales YTD | Sales OBQ | Sales CBQ |
| --- | --- | --- | --- | --- | --- |
| Q1-2007 | 20071 | 2,646,673.39 | 2,646,673.39 | (Blank) | 11,309,946.12 |
| Q2-2007 | 20072 | 3,046,602.02 | 5,693,275.41 | (Blank) | 11,309,946.12 |
| Q3-2007 | 20073 | 2,885,246.55 | 8,578,521.96 | (Blank) | 11,309,946.12 |
| Q4-2007 | 20074 | 2,731,424.16 | 11,309,946.12 | (Blank) | 11,309,946.12 |
| Q1-2008 | 20081 | 1,816,385.21 | 1,816,385.21 | 11,309,946.12 | 9,927,582.99 |
| Q2-2008 | 20082 | 2,738,040.73 | 4,554,425.94 | 11,309,946.12 | 9,927,582.99 |
| Q3-2008 | 20083 | 2,575,545.59 | 7,129,971.53 | 11,309,946.12 | 9,927,582.99 |
| Q4-2008 | 20084 | 2,797,611.46 | 9,927,582.99 | 11,309,946.12 | 9,927,582.99 |

## Related articles

Learn more about OPENINGBALANCEYEAR in the following articles:

- [**Semi-Additive Measures in DAX**](https://www.sqlbi.com/articles/semi-additive-measures-in-dax/)

  Values such as inventory and balance account, usually calculated from a snapshot table, require the use of semi-additive measures. In Multidimensional you have specific aggregation types, like LastChild and LastNonEmpty. In PowerPivot and Tabular you use DAX, which is flexible enough to implement any calculation, as described in this article. [» Read more](https://www.sqlbi.com/articles/semi-additive-measures-in-dax/)

## Related functions

Other related functions are:

- [[CLOSINGBALANCEMONTH]]
- [[CLOSINGBALANCEQUARTER]]
- [[CLOSINGBALANCEYEAR]]
- [[OPENINGBALANCEMONTH]]
- [[OPENINGBALANCEQUARTER]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Steve Yamashiro

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/openingbalanceyear-function-dax](https://docs.microsoft.com/en-us/dax/openingbalanceyear-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
