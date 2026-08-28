---
title: "USERPRINCIPALNAME"
function: "userprincipalname"
category: "Information"
url: "https://dax.guide/userprincipalname/"
source: "dax.guide"
重要度:
难度:
---

# USERPRINCIPALNAME DAX Function (Information) Volatile

Returns the user principal name.

## Syntax

USERPRINCIPALNAME ( )

This expression has no parameters.

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

Returns the name of the user as their email address, aka user@domain.com.

## Remarks

This function is not supported in calculated tables/columns.

Despite the metadata exposed, this function does not work on Analysis Services 2016.

[» 4 related articles](#articles)  
[» 3 related functions](#alt)  

## Examples

```dax


--  USERNAME is the local user name (same as USERPRINCIPALNAME using 

--  Azure AS and Power BI Service)

--  USEROBJECTID is the security identifier in Active Directory and 

--  Azure Active Directory

--  USERPRINCIPALNAME is the user principal name, typically the user 

--  email (it could be different in Active Directory on-premises)

EVALUATE

{

    ( "USERNAME",           USERNAME () ),

    ( "USEROBJECTID",       USEROBJECTID () ),

    ( "USEPRINCIPALNAME",   USERPRINCIPALNAME () )

}

```

## Related articles

Learn more about USERPRINCIPALNAME in the following articles:

- [**Reading active Power BI security roles in DAX**](https://www.sqlbi.com/articles/reading-active-power-bi-security-roles-in-dax/)

  This article describes how to read the active security roles in a Tabular model for Power BI or Analysis Services. This way, you can use measures and calculation groups to customize a report based dynamically on security roles active for the current user. [» Read more](https://www.sqlbi.com/articles/reading-active-power-bi-security-roles-in-dax/)
- [**Customizing default values for each user in Power BI reports**](https://www.sqlbi.com/articles/customizing-default-values-for-each-user-in-power-bi-reports/)

  This article shows how to use calculation groups to define a default set of values for columns in your model. Different users can have different default values, and yet retain the full capability to select different values. [» Read more](https://www.sqlbi.com/articles/customizing-default-values-for-each-user-in-power-bi-reports/)
- [**DAX limitations with inactive relationships and row-level security (RLS)**](https://www.sqlbi.com/articles/dax-limitations-with-inactive-relationships-and-row-level-security-rls/)

  When you apply row-level security to a semantic model, there are limitations in using the USERELATIONSHIP function. This article shows the issues, provides a workaround, and its restrictions. [» Read more](https://www.sqlbi.com/articles/dax-limitations-with-inactive-relationships-and-row-level-security-rls/)
- [**Introducing user-aware calculated columns in Power BI**](https://www.sqlbi.com/articles/introducing-user-aware-calculated-columns-in-power-bi/)

  This article describes the new Expression Context property of calculated columns in Power BI, explaining how user-aware calculated columns work, why they are not materialized, and how to use them as virtual calculated columns for localization and custom security scenarios. [» Read more](https://www.sqlbi.com/articles/introducing-user-aware-calculated-columns-in-power-bi/)

## Related functions

Other related functions are:

- [[USERNAME]]
- [[USEROBJECTID]]
- [[USERCULTURE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Gert Christen

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/userprincipalname-function-dax](https://docs.microsoft.com/en-us/dax/userprincipalname-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
