---
title: "CUSTOMDATA"
function: "customdata"
category: "Information"
url: "https://dax.guide/customdata/"
source: "dax.guide"
重要度:
难度:
---

# CUSTOMDATA DAX Function (Information) Volatile

Returns the value of the CustomData connection string property if defined; otherwise, [[BLANK]]().

## Syntax

CUSTOMDATA ( )

This expression has no parameters.

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

The content of the CustomData property in the connection string.  
Blank, if CustomData property was not defined at connection time.

## Remarks

This function is commonly used in implementing expressions for role-based security when an application uses custom authentication.

This function is not supported in calculated tables/columns.

**IMPORTANT**: there are reported performance issues if CUSTOMDATA is used in DAX expressions of the model, including RLS filters and measures, in case the connection string does not include CustomData argument. When this happens, simply passing a value to CustomData in the connection string eliminates the performance issue.

[» 1 related article](#articles)  

## Examples

```dax


--  CUSTOMDATA returns the CUSTOMDATA argument provided in the 

--  connection string.

--

--  It is mainly useful in custom security systems where the

--  caller uses CUSTOMDATA to communicate security parameters

--  to the AS DAX system

EVALUATE

{

    ( "CUSTOMDATA", CUSTOMDATA () )

}

```

## Related articles

Learn more about CUSTOMDATA in the following articles:

- [**Introducing user-aware calculated columns in Power BI**](https://www.sqlbi.com/articles/introducing-user-aware-calculated-columns-in-power-bi/)

  This article describes the new Expression Context property of calculated columns in Power BI, explaining how user-aware calculated columns work, why they are not materialized, and how to use them as virtual calculated columns for localization and custom security scenarios. [» Read more](https://www.sqlbi.com/articles/introducing-user-aware-calculated-columns-in-power-bi/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/customdata-function-dax](https://docs.microsoft.com/en-us/dax/customdata-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
