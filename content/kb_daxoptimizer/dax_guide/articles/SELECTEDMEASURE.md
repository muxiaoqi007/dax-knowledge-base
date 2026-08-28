---
title: "SELECTEDMEASURE"
function: "selectedmeasure"
category: "Information"
url: "https://dax.guide/selectedmeasure/"
source: "dax.guide"
重要度:
难度:
---

# SELECTEDMEASURE DAX Function (Information) Context Transition

Returns the measure that is currently being evaluated.

## Syntax

SELECTEDMEASURE ( )

This expression has no parameters.

## Return values

Scalar A single value of any type.

A reference to the measure that is currently in context when the calculation item is evaluated.

[» 13 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


-- Code of the calculation item Growth in the Time calc

-- calculation group used in the following example.

IF (

    NOT ISSELECTEDMEASURE ( [Pct over all prods] ),

    VAR CY =

        SELECTEDMEASURE ()

    VAR PY =

        CALCULATE (

            SELECTEDMEASURE (),

            SAMEPERIODLASTYEAR ( 'Date'[Date] )

        )

    VAR Result = CY - PY

    RETURN

        Result

)

```

```dax


--  SELECTEDMEASURE is a placeholder used in calculation items

--  to represent the measure currently being modified by the

--  calculation item.

--

--  During the application process, each reference to SELECTEDMEASURE

--  is replaced with the currently selected measure

DEFINE

    MEASURE Sales[Sales Amount] =

        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

    MEASURE Sales[Sales Quantity] =

        SUM ( Sales[Quantity] )

EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Calendar Year],

    'Time calc'[Time calc],

    TREATAS ( { "CY 2008" }, 'Date'[Calendar Year] ),

    "Sales Amount", [Sales Amount],

    "Quantity", [Sales Quantity]

)

ORDER BY

    'Date'[Calendar Year],

    'Time calc'[Time calc]

```

| Calendar Year | Time calc | Sales Amount | Quantity |
| --- | --- | --- | --- |
| 2008-01-01 | Current | 9,927,582.99 | 40,226.00 |
| 2008-01-01 | Growth | -1,382,363.13 | -4,084.00 |
| 2008-01-01 | Growth % | -0.12 | -0.09 |
| 2008-01-01 | Prev Year | 11,309,946.12 | 44,310.00 |

## Related articles

Learn more about SELECTEDMEASURE in the following articles:

- [**Introducing Calculation Groups**](https://www.sqlbi.com/articles/introducing-calculation-groups/)

  This article is the first of a series dedicated to calculation groups in DAX. This introduction explains the capabilities of this feature and how to create calculation groups in a Tabular model. [» Read more](https://www.sqlbi.com/articles/introducing-calculation-groups/)
- [**Understanding Calculation Groups**](https://www.sqlbi.com/articles/understanding-calculation-groups/)

  This article explores the properties of calculation groups in detail and then it describes how a calculation item is applied to a measure. Before starting, we suggest you read the previous article that introduces calculation groups. [» Read more](https://www.sqlbi.com/articles/understanding-calculation-groups/)
- [**Understanding the Application of Calculation Items**](https://www.sqlbi.com/articles/understanding-the-application-of-calculation-items/)

  This article explains how calculation items are applied to measure references, and it is part of a series dedicated to calculation groups in DAX. Before starting, we suggest you read the previous articles in this series. [» Read more](https://www.sqlbi.com/articles/understanding-the-application-of-calculation-items/)
- [**Understanding Calculation Group Precedence**](https://www.sqlbi.com/articles/understanding-calculation-group-precedence/)

  This article explains the precedence of calculation groups in DAX, needed whenever multiple calculation groups are present within the same model. Before starting, we suggest you read the previous articles in this series. [» Read more](https://www.sqlbi.com/articles/understanding-calculation-group-precedence/)
- [**Avoiding Pitfalls in Calculation Groups Precedence**](https://www.sqlbi.com/articles/avoiding-pitfalls-in-calculation-groups-precedence/)

  This article describes in which conditions the precedence of calculation groups might return unexpected results when filtering calculation items in both the visuals and the measures present in a report. [» Read more](https://www.sqlbi.com/articles/avoiding-pitfalls-in-calculation-groups-precedence/)
- [**Computing MTD, QTD, YTD in Power BI for the current period**](https://www.sqlbi.com/articles/computing-mtd-qtd-ytd-in-power-bi-for-the-current-period/)

  This article describes how to use the DAX time intelligence calculations applied to the latest period available in the data, also known as the “current” period. [» Read more](https://www.sqlbi.com/articles/computing-mtd-qtd-ytd-in-power-bi-for-the-current-period/)
- [**Using field parameters and calculation groups for conditional formatting**](https://www.sqlbi.com/articles/using-field-parameters-and-calculation-groups-for-conditional-formatting/)

  This article describes how to apply conditional formatting on measures picked from a slicer and implemented using two techniques: field parameters and calculation groups. [» Read more](https://www.sqlbi.com/articles/using-field-parameters-and-calculation-groups-for-conditional-formatting/)
- [**Using calculation groups or many-to-many relationships for time intelligence selection**](https://www.sqlbi.com/articles/using-calculation-groups-or-many-to-many-relationships-for-time-intelligence-selection/)

  This article compares two common techniques to filter time periods in DAX: calculation groups and many-to-many relationships. [» Read more](https://www.sqlbi.com/articles/using-calculation-groups-or-many-to-many-relationships-for-time-intelligence-selection/)
- [**Scripting syntax for calculation groups**](https://www.sqlbi.com/articles/scripting-syntax-for-calculation-groups/)

  This article introduces the syntax to describe in a textual form the DAX expressions and additional properties of calculation groups. [» Read more](https://www.sqlbi.com/articles/scripting-syntax-for-calculation-groups/)
- [**Understanding the interactions between composite models and calculation groups**](https://www.sqlbi.com/articles/understanding-the-interactions-between-composite-models-and-calculation-groups/)

  When used in a composite model, calculation groups show a very unique behavior that a good DAX developer must understand well to build sound models. In this article we describe how composite models and calculation groups work together. [» Read more](https://www.sqlbi.com/articles/understanding-the-interactions-between-composite-models-and-calculation-groups/)
- [**Customizing default values for each user in Power BI reports**](https://www.sqlbi.com/articles/customizing-default-values-for-each-user-in-power-bi-reports/)

  This article shows how to use calculation groups to define a default set of values for columns in your model. Different users can have different default values, and yet retain the full capability to select different values. [» Read more](https://www.sqlbi.com/articles/customizing-default-values-for-each-user-in-power-bi-reports/)
- [**Using calculation groups to selectively replace measures in DAX expressions**](https://www.sqlbi.com/articles/using-calculation-groups-to-selectively-replace-measures-in-dax-expressions/)

  This article describes how to use calculation groups to dynamically replace only a partial expression in a complex DAX calculation. [» Read more](https://www.sqlbi.com/articles/using-calculation-groups-to-selectively-replace-measures-in-dax-expressions/)
- [**Sideways recursion in DAX calculation groups**](https://www.sqlbi.com/articles/sideways-recursion-in-dax-calculation-groups/)

  This article describes the sideways recursion triggered by invoking a calculation item from another calculation item, explaining why it should be avoided to steer clear of unexpected results. [» Read more](https://www.sqlbi.com/articles/sideways-recursion-in-dax-calculation-groups/)

## Related functions

Other related functions are:

- [[SELECTEDMEASURENAME]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/selectedmeasure-function-dax](https://docs.microsoft.com/en-us/dax/selectedmeasure-function-dax ?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
