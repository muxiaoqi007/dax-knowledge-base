---
title: "ISSELECTEDMEASURE"
function: "isselectedmeasure"
category: "Information"
url: "https://dax.guide/isselectedmeasure/"
source: "dax.guide"
重要度:
难度:
---

# ISSELECTEDMEASURE DAX Function (Information)

Returns true if one of the specified measures is currently being evaluated.

## Syntax

ISSELECTEDMEASURE ( <Measure> [, <Measure> [, … ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Measure | Repeatable | Measure,… |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

True whether the measure that is currently in context is one of those specified in the list of parameters.

## Remarks

ISSELECTEDMEASURE does not recognize a query measure. Therefore, if a model measure is redefined and used in a DAX query, ISSELECTEDMEASURE does not recognize that measure, whereas [[SELECTEDMEASURENAME]] still correctly intercepts the measure name.

[» 3 related articles](#articles)  

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


--  ISSELECTEDMEASURE checks whether the current SELECTEDMEASURE is 

--  in the list of its arguments.

--

--  It can be used only in calculation items to modify the behavior

--  of the calculation item application depending on the currently

--  selected measure.

DEFINE

    MEASURE Sales[Sales Amount] =

        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

    MEASURE Sales[Sales Quantity] =

        SUM ( Sales[Quantity] )

EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Brand],

    'Date'[Calendar Year],

    'Time calc'[Time calc],

    TREATAS ( { "CY 2008" }, 'Date'[Calendar Year] ),

    TREATAS ( { "Contoso", "Fabrikam" }, 'Product'[Brand] ),

    "Sales Amount", [Sales Amount],

    "Quantity", [Sales Quantity],

    "% of Products", [Pct over all prods]

)

ORDER BY

    'Product'[Brand],

    'Date'[Calendar Year],

    'Time calc'[Time calc]

```

| Brand | Calendar Year | Time calc | Sales Amount | Quantity | % of Products |
| --- | --- | --- | --- | --- | --- |
| Contoso | 2008-01-01 | Current | 2,369,167.68 | 14,901.00 | 54.31% |
| Contoso | 2008-01-01 | Growth | -360,650.85 | 429.00 | (Blank) |
| Contoso | 2008-01-01 | Growth % | -0.13 | 0.03 | (Blank) |
| Contoso | 2008-01-01 | Prev Year | 2,729,818.54 | 14,472.00 | 62.29% |
| Fabrikam | 2008-01-01 | Current | 1,993,123.48 | 3,899.00 | 45.69% |
| Fabrikam | 2008-01-01 | Growth | 340,372.14 | 701.00 | (Blank) |
| Fabrikam | 2008-01-01 | Growth % | 0.21 | 0.22 | (Blank) |
| Fabrikam | 2008-01-01 | Prev Year | 1,652,751.34 | 3,198.00 | 37.71% |

## Related articles

Learn more about ISSELECTEDMEASURE in the following articles:

- [**Introducing Calculation Groups**](https://www.sqlbi.com/articles/introducing-calculation-groups/)

  This article is the first of a series dedicated to calculation groups in DAX. This introduction explains the capabilities of this feature and how to create calculation groups in a Tabular model. [» Read more](https://www.sqlbi.com/articles/introducing-calculation-groups/)
- [**Using field parameters and calculation groups for conditional formatting**](https://www.sqlbi.com/articles/using-field-parameters-and-calculation-groups-for-conditional-formatting/)

  This article describes how to apply conditional formatting on measures picked from a slicer and implemented using two techniques: field parameters and calculation groups. [» Read more](https://www.sqlbi.com/articles/using-field-parameters-and-calculation-groups-for-conditional-formatting/)
- [**Using calculation groups to selectively replace measures in DAX expressions**](https://www.sqlbi.com/articles/using-calculation-groups-to-selectively-replace-measures-in-dax-expressions/)

  This article describes how to use calculation groups to dynamically replace only a partial expression in a complex DAX calculation. [» Read more](https://www.sqlbi.com/articles/using-calculation-groups-to-selectively-replace-measures-in-dax-expressions/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/isselectedmeasure-function-dax](https://docs.microsoft.com/en-us/dax/isselectedmeasure-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
