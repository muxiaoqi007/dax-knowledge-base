---
title: "SELECTEDMEASURENAME"
function: "selectedmeasurename"
category: "Information"
url: "https://dax.guide/selectedmeasurename/"
source: "dax.guide"
重要度:
难度:
---

# SELECTEDMEASURENAME DAX Function (Information)

Returns name of the measure that is currently being evaluated.

## Syntax

SELECTEDMEASURENAME ( )

This expression has no parameters.

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

Returns the name of the measure evaluated.

[» 5 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


-- Code of the calculation item Growth in the Time calc

-- calculation group used in the following example.

IF (

    SEARCH ( "Pct", SELECTEDMEASURENAME (), 1, -1 ) = -1,

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


--  SELECTEDMEASURENAME returns the name of the currently selected measure.

--  It can be used in place of ISSELECTEDMEASURE to search for specific

--  name patterns.

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

Learn more about SELECTEDMEASURENAME in the following articles:

- [**Introducing Calculation Groups**](https://www.sqlbi.com/articles/introducing-calculation-groups/)

  This article is the first of a series dedicated to calculation groups in DAX. This introduction explains the capabilities of this feature and how to create calculation groups in a Tabular model. [» Read more](https://www.sqlbi.com/articles/introducing-calculation-groups/)
- [**Currency conversion in Power BI reports**](https://www.sqlbi.com/articles/currency-conversion-in-power-bi-reports/)

  This article describes how to implement currency conversion for reporting purposes in Power BI. [» Read more](https://www.sqlbi.com/articles/currency-conversion-in-power-bi-reports/)
- [**Controlling Format Strings in Calculation Groups**](https://www.sqlbi.com/articles/controlling-format-strings-in-calculation-groups/)

  This article describes how to control format strings in calculation groups. Before starting, we suggest you read the previous articles in this series. [» Read more](https://www.sqlbi.com/articles/controlling-format-strings-in-calculation-groups/)
- [**Scripting syntax for calculation groups**](https://www.sqlbi.com/articles/scripting-syntax-for-calculation-groups/)

  This article introduces the syntax to describe in a textual form the DAX expressions and additional properties of calculation groups. [» Read more](https://www.sqlbi.com/articles/scripting-syntax-for-calculation-groups/)
- [**Controlling empty or multiple selections in calculation groups**](https://www.sqlbi.com/articles/controlling-empty-or-multiple-selections-in-calculation-groups/)

  This article describes how to control the execution of DAX code when there are either multiple or empty selections of calculation items in calculation groups. [» Read more](https://www.sqlbi.com/articles/controlling-empty-or-multiple-selections-in-calculation-groups/)

## Related functions

Other related functions are:

- [[SELECTEDMEASURE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/selectedmeasurename-function-dax](https://docs.microsoft.com/en-us/dax/selectedmeasurename-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
