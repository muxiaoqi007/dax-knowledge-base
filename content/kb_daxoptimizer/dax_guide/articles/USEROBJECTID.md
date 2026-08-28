---
title: "USEROBJECTID"
function: "userobjectid"
category: "Information"
url: "https://dax.guide/userobjectid/"
source: "dax.guide"
重要度:
难度:
---

# USEROBJECTID DAX Function (Information) Volatile

Returns the current user’s Object ID from Azure AD for Azure Analysis Server and the current user’s SID for on-premise Analysis Server.

## Syntax

USEROBJECTID ( )

This expression has no parameters.

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

It is the security identifier (SID) in Windows, and another identifier in the Power BI or Azure Analysis Services service.

This function is not supported in calculated tables/columns.

[» 1 related article](#articles)  
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

Learn more about USEROBJECTID in the following articles:

- [**Introducing user-aware calculated columns in Power BI**](https://www.sqlbi.com/articles/introducing-user-aware-calculated-columns-in-power-bi/)

  This article describes the new Expression Context property of calculated columns in Power BI, explaining how user-aware calculated columns work, why they are not materialized, and how to use them as virtual calculated columns for localization and custom security scenarios. [» Read more](https://www.sqlbi.com/articles/introducing-user-aware-calculated-columns-in-power-bi/)

## Related functions

Other related functions are:

- [[USERNAME]]
- [[USERPRINCIPALNAME]]
- [[USERCULTURE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/userobjectid-function-dax](https://docs.microsoft.com/en-us/dax/userobjectid-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
