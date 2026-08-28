---
title: "USERNAME"
function: "username"
category: "Information"
url: "https://dax.guide/username/"
source: "dax.guide"
重要度:
难度:
---

# USERNAME DAX Function (Information) Volatile

Returns the domain name and user name of the current connection with the format of domain-name\user-name.

## Syntax

USERNAME ( )

This expression has no parameters.

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

The username from the credentials given to the system at connection time.

This function is not supported in calculated tables/columns.

[» 3 related articles](#articles)  
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

Learn more about USERNAME in the following articles:

- [**Managing hierarchical organizations in Power BI security roles**](https://www.sqlbi.com/articles/managing-hierarchical-organizations-in-power-bi-security-roles/)

  This article describes how to apply dynamic security roles in a hierarchical organization to minimize the maintenance effort on the security configuration and obtain the best performance at query time. [» Read more](https://www.sqlbi.com/articles/managing-hierarchical-organizations-in-power-bi-security-roles/)
- [**Reading active Power BI security roles in DAX**](https://www.sqlbi.com/articles/reading-active-power-bi-security-roles-in-dax/)

  This article describes how to read the active security roles in a Tabular model for Power BI or Analysis Services. This way, you can use measures and calculation groups to customize a report based dynamically on security roles active for the current user. [» Read more](https://www.sqlbi.com/articles/reading-active-power-bi-security-roles-in-dax/)
- [**Introducing user-aware calculated columns in Power BI**](https://www.sqlbi.com/articles/introducing-user-aware-calculated-columns-in-power-bi/)

  This article describes the new Expression Context property of calculated columns in Power BI, explaining how user-aware calculated columns work, why they are not materialized, and how to use them as virtual calculated columns for localization and custom security scenarios. [» Read more](https://www.sqlbi.com/articles/introducing-user-aware-calculated-columns-in-power-bi/)

## Related functions

Other related functions are:

- [[USEROBJECTID]]
- [[USERPRINCIPALNAME]]
- [[USERCULTURE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/username-function-dax](https://docs.microsoft.com/en-us/dax/username-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
