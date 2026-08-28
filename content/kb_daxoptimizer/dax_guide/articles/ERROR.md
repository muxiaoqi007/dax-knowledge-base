---
title: "ERROR"
function: "error"
url: "https://dax.guide/error/"
source: "dax.guide"
重要度:
难度:
---

# ERROR DAX Function

Raises a user specified error.

## Syntax

ERROR ( <ErrorText> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ErrorText |  | The text of the error to be raised. |

## Return values

The function does not return any value.

## Remarks

This function stops the execution raising an error with the description provided as argument.  
The ERROR function can be placed in a DAX expression anywhere a scalar value is expected.  
Because ERROR does not define a data type, use [[CONVERT]] around ERROR to control the expected data type and avoid the variant data type of the resulting expression.

[» 7 related articles](#articles)  

## Examples

```dax


--  ERROR produces an error to inform the users about a calculation

--  that needs to be aborted.

DEFINE MEASURE 

    Sales[Inflation Rate] = 

        CALCULATE (

            SELECTEDVALUE ( 

                'Rates'[InflationRate],

                CONVERT ( ERROR ( "Missing inflation rate" ), DOUBLE )

            ),

            TREATAS (

                VALUES ( 'Date'[Calendar Year Number] ),

                'Rates'[Year]

            )

        )

EVALUATE 'Rates'



EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Calendar Year],

    "Rate%", [Inflation Rate]

)

```

## Related articles

Learn more about ERROR in the following articles:

- [**Automatic time intelligence in Power BI**](https://www.sqlbi.com/articles/automatic-time-intelligence-in-power-bi/)

  This article shows why building custom Date tables is preferable to using the automatic date/time handling capabilities of Power BI. [» Read more](https://www.sqlbi.com/articles/automatic-time-intelligence-in-power-bi/)
- [**Using the SELECTEDVALUE function in DAX**](https://www.sqlbi.com/articles/using-the-selectedvalue-function-in-dax/)

  This article describes how the SELECTEDVALUE DAX function simplifies the syntax required in many scenarios where you need to read a single value selected in the filter context. [» Read more](https://www.sqlbi.com/articles/using-the-selectedvalue-function-in-dax/)
- [**Comparing different school terms in Power BI**](https://www.sqlbi.com/articles/comparing-different-school-terms-in-power-bi/)

  This article describes how to implement the comparison between school terms. The same technique can be applied to any arbitrary time periods that do not match regular months or quarters in a calendar, such as seasons or campaigns. [» Read more](https://www.sqlbi.com/articles/comparing-different-school-terms-in-power-bi/)
- [**Reading active Power BI security roles in DAX**](https://www.sqlbi.com/articles/reading-active-power-bi-security-roles-in-dax/)

  This article describes how to read the active security roles in a Tabular model for Power BI or Analysis Services. This way, you can use measures and calculation groups to customize a report based dynamically on security roles active for the current user. [» Read more](https://www.sqlbi.com/articles/reading-active-power-bi-security-roles-in-dax/)
- [**When are variables evaluated in DAX?**](https://www.sqlbi.com/articles/when-are-variables-evaluated-in-dax/)

  This article clarifies how DAX evaluates variables, which is important to avoid common mistakes when using DAX for the first time. [» Read more](https://www.sqlbi.com/articles/when-are-variables-evaluated-in-dax/)
- [**Dynamic Pareto analysis in Power BI**](https://www.sqlbi.com/articles/dynamic-pareto-analysis-in-power-bi/)

  This article describes how to implement a dynamic Pareto calculation in Power BI based on a measure that can be selected from a slicer and dynamically filtered by other slicers in the report. [» Read more](https://www.sqlbi.com/articles/dynamic-pareto-analysis-in-power-bi/)
- [**Controlling empty or multiple selections in calculation groups**](https://www.sqlbi.com/articles/controlling-empty-or-multiple-selections-in-calculation-groups/)

  This article describes how to control the execution of DAX code when there are either multiple or empty selections of calculation items in calculation groups. [» Read more](https://www.sqlbi.com/articles/controlling-empty-or-multiple-selections-in-calculation-groups/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Pär Adeen

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/error-function](https://docs.microsoft.com/en-us/dax/error-function?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
