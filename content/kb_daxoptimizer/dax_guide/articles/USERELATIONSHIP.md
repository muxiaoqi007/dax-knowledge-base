---
title: "USERELATIONSHIP"
function: "userelationship"
category: "Relationship management"
url: "https://dax.guide/userelationship/"
source: "dax.guide"
重要度:
难度:
---

# USERELATIONSHIP DAX Function (Relationship management)

Specifies an existing relationship to be used in the evaluation of a DAX expression. The relationship is defined by naming, as arguments, the two columns that serve as endpoints.

## Syntax

USERELATIONSHIP ( <ColumnName1>, <ColumnName2> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName1 |  | Foreign (or primary) key of the relationship. |
| ColumnName2 |  | Primary (or foreign) key of the relationship. |

## Return values

The function returns no value; the function only enables the indicated relationship for the duration of the calculation.

## Remarks

USERELATIONSHIP can only be used in functions that take a filter predicate as an argument, for example: [[CALCULATE]], [[CALCULATETABLE]], [[CLOSINGBALANCEMONTH]], [[CLOSINGBALANCEQUARTER]], [[CLOSINGBALANCEYEAR]], [[OPENINGBALANCEMONTH]], [[OPENINGBALANCEQUARTER]], [[OPENINGBALANCEYEAR]], [[TOTALMTD]], [[TOTALQTD]] and [[TOTALYTD]] functions.

USERELATIONSHIP uses existing relationships in the model, identifying relationships by their ending point columns.

In USERELATIONSHIP, the status of a relationship is not important; that is, whether the relationship is active or not does not affect the usage of the function. Even if the relationship is inactive, it will be used and overrides any other active relationships that might be present in the model but not mentioned in the function arguments.

An error is returned if any of the columns named as an argument is not part of a relationship or the arguments belong to different relationships.

If [[CALCULATE]] expressions are nested, and more than one [[CALCULATE]] expression contains a USERELATIONSHIP function, then the innermost USERELATIONSHIP is the one that prevails in case of a conflict or ambiguity.

USERELATIONSHIP cannot be used when it changes the default filter propagation from a table that has Row-level security (RLS) applied. Consider using [[TREATAS]] as a workaround in those cases.

[» 7 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  USERELATIONSHIP activates a disabled relationship, deactivating

--  possible conflicting relationships.

--

--  Useful when the model contains inactive relationships to handle

--  role-playing dimensions.

DEFINE

    MEASURE Sales[Delivery Amount] =

        CALCULATE (

            [Sales Amount],

            USERELATIONSHIP ( Sales[Delivery Date], 'Date'[Date] )

        )

EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Calendar Year],

    "Sales Amount", [Sales Amount],

    "Delivery Amount", [Delivery Amount]

)

```

| Calendar Year | Sales Amount | Delivery Amount |
| --- | --- | --- |
| 2007-01-01 | 11,309,946.12 | 11,034,860.43 |
| 2008-01-01 | 9,927,582.99 | 9,901,407.94 |
| 2009-01-01 | 9,353,814.87 | 9,442,286.09 |
| 2010-01-01 | (Blank) | 212,789.51 |

## Related articles

Learn more about USERELATIONSHIP in the following articles:

- [**Using USERELATIONSHIP in DAX**](https://www.sqlbi.com/articles/using-userelationship-in-dax/)

  This article shows how to use the USERELATIONSHIP function in DAX to change the active relationship in a CALCULATE function. [» Read more](https://www.sqlbi.com/articles/using-userelationship-in-dax/)
- [**USERELATIONSHIP in Calculated Columns**](https://www.sqlbi.com/articles/userelationship-in-calculated-columns/)

  In a Power Pivot or Tabular model with inactive relationships, one can rely on the USERELATIONSHIP function to apply an inactive relationship to a particular DAX expression. Its usage is simple in a measure, but one might consider alternative syntax… [» Read more](https://www.sqlbi.com/articles/userelationship-in-calculated-columns/)
- [**Relationships in Power BI and Tabular models**](https://www.sqlbi.com/articles/relationships-in-power-bi-and-tabular-models/)

  This article describes the types of relationships available in Power BI and Analysis Services, clarifying the differences in cardinality and filter propagation of physical relationships. [» Read more](https://www.sqlbi.com/articles/relationships-in-power-bi-and-tabular-models/)
- [**Using cross-highlight with order and delivery dates in Power BI**](https://www.sqlbi.com/articles/using-cross-highlight-with-order-and-delivery-dates-in-power-bi/)

  This article describes how to enable the cross-highlight in Power BI charts using different dates for the same event, such as Order Date and Delivery Date. [» Read more](https://www.sqlbi.com/articles/using-cross-highlight-with-order-and-delivery-dates-in-power-bi/)
- [**Different options to model many-to-many relationships in Power BI and Tabular**](https://www.sqlbi.com/articles/different-options-to-model-many-to-many-relationships-in-power-bi-and-tabular/)

  There are two options to model many-to-many relationships using Tabular and Power BI: you can use either a regular bidirectional filter relationship, or a limited unidirectional relationship. In this article, we compare the performance of both options. [» Read more](https://www.sqlbi.com/articles/different-options-to-model-many-to-many-relationships-in-power-bi-and-tabular/)
- [**Using calculation groups to switch between dates**](https://www.sqlbi.com/articles/using-calculation-groups-to-switch-between-dates/)

  This article shows how to use calculation groups to change the active relationship in a model in order to let users choose among multiple dates. [» Read more](https://www.sqlbi.com/articles/using-calculation-groups-to-switch-between-dates/)
- [**DAX limitations with inactive relationships and row-level security (RLS)**](https://www.sqlbi.com/articles/dax-limitations-with-inactive-relationships-and-row-level-security-rls/)

  When you apply row-level security to a semantic model, there are limitations in using the USERELATIONSHIP function. This article shows the issues, provides a workaround, and its restrictions. [» Read more](https://www.sqlbi.com/articles/dax-limitations-with-inactive-relationships-and-row-level-security-rls/)

## Related functions

Other related functions are:

- [[CROSSFILTER]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/userelationship-function-dax](https://docs.microsoft.com/en-us/dax/userelationship-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
