---
title: "CALCULATETABLE"
function: "calculatetable"
category: "Filter"
url: "https://dax.guide/calculatetable/"
source: "dax.guide"
重要度:
难度:
---

# CALCULATETABLE DAX Function (Filter) Context Transition

Evaluates a table expression in a context modified by filters.

## Syntax

CALCULATETABLE ( <Table> [, <Filter> [, <Filter> [, … ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table  By Expression |  | The table expression to be evaluated. |
| Filter | Optional Repeatable | A boolean (True/False) expression or a table expression that defines a filter. |

## Return values

Table An entire table or a table with one or more columns.

The value is the result of the expression evaluated in a modified filter context.

## Remarks

Every filter argument can be either a filter removal (such as [[ALL]], [[ALLEXCEPT]], [[ALLNOBLANKROW]]), a filter restore ([[ALLSELECTED]]), or a table expression returning a list of values for one or more columns or for an entire expanded table.

When a filter argument has the form of a predicate with a single column reference, the expression is embedded into a [[FILTER]] expression that filters all the values of the referenced column. For example, the predicate shown in the first expression is internally converted in the second expression.

```dax


CALCULATETABLE ( 

    <table_expression>,

    table[column] = 10

)



CALCULATETABLE ( 

    <table_expression>,

    FILTER ( 

        ALL ( table[column] ),

        table[column] = 10

    )

)

```

A filter argument overrides the existing corresponding filters over the same column(s), unless it is embedded within [[KEEPFILTERS]].

CALCULATETABLE follow the same steps of [[CALCULATE]] to evaluate its result.

[» 7 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

CALCULATETABLE is identical to CALCULATE, except for the result: it returns a table instead of a scalar value.

```dax


-- Returns the colors of Proseware branded products

EVALUATE

CALCULATETABLE (

    VALUES ( 'Product'[Color] ),

    'Product'[Brand] = "Proseware"

)

```

| Color |
| --- |
| Silver |
| Blue |
| White |
| Red |
| Black |
| Green |
| Grey |

## Related articles

Learn more about CALCULATETABLE in the following articles:

- [**FILTER vs CALCULATETABLE: optimization using cardinality estimation**](https://www.sqlbi.com/articles/filter-vs-calculatetable-optimization-using-cardinality-estimation/)

  A common best practice is to use CALCULATETABLE instead of FILTER for performance reasons. This article explores the reasons why and explains when FILTER might be better than CALCULATETABLE. [» Read more](https://www.sqlbi.com/articles/filter-vs-calculatetable-optimization-using-cardinality-estimation/)
- [**Managing “all” functions in DAX: ALL, ALLSELECTED, ALLNOBLANKROW, ALLEXCEPT**](https://www.sqlbi.com/articles/managing-all-functions-in-dax-all-allselected-allnoblankrow-allexcept/)

  This article provides a complete explanation of the behavior of the ALLxxx functions in DAX. When used as filters in CALCULATE, ALLxxx functions might display unexpected behaviors. [» Read more](https://www.sqlbi.com/articles/managing-all-functions-in-dax-all-allselected-allnoblankrow-allexcept/)
- [**Expanded tables in DAX**](https://www.sqlbi.com/articles/expanded-tables-in-dax/)

  Expanded tables are the core of DAX; understanding how they work is of paramount importance. This article provides a theoretical foundation of what expanded tables are, along with fundamental concepts useful when reading DAX code. [» Read more](https://www.sqlbi.com/articles/expanded-tables-in-dax/)
- [**From SQL to DAX: Filtering Data**](https://www.sqlbi.com/articles/from-sql-to-dax-filtering-data/)

  The WHERE condition of an SQL statement has two counterparts in DAX: FILTER and CALCULATETABLE. In this article we explore the differences between them, providing a few best practices in their use. [» Read more](https://www.sqlbi.com/articles/from-sql-to-dax-filtering-data/)
- [**Understanding context transition in DAX**](https://www.sqlbi.com/articles/understanding-context-transition-in-dax/)

  Context transition is one of the most obscure topics for DAX newbies. In this article we introduce context transition, its effects, and how to leverage it rather than be scared of it. [» Read more](https://www.sqlbi.com/articles/understanding-context-transition-in-dax/)
- [**Using RELATED and RELATEDTABLE in DAX**](https://www.sqlbi.com/articles/using-related-and-relatedtable-in-dax/)

  RELATED and its companion function RELATEDTABLE, are two common DAX functions that are required when using a row context with relationships. In this article we describe why and when to use these two functions. [» Read more](https://www.sqlbi.com/articles/using-related-and-relatedtable-in-dax/)
- [**Filter columns, not tables, in DAX**](https://www.sqlbi.com/articles/filter-columns-not-tables-in-dax/)

  One of the few golden rules in DAX is to always filter columns and never filter tables with CALCULATE. This article explains the rationale behind the rule. [» Read more](https://www.sqlbi.com/articles/filter-columns-not-tables-in-dax/)

## Related functions

Other related functions are:

- [[CALCULATE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/calculatetable-function-dax](https://docs.microsoft.com/en-us/dax/calculatetable-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
