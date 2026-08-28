---
title: "USERCULTURE"
function: "userculture"
category: "Information"
url: "https://dax.guide/userculture/"
source: "dax.guide"
重要度:
难度:
---

# USERCULTURE DAX Function (Information) Volatile

Returns the culture code for the user, based on their operating system or browser settings.

## Syntax

USERCULTURE ( )

This expression has no parameters.

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

The culture code as a string, such as “en-US”.

[» 2 related articles](#articles)  
[» 3 related functions](#alt)  

## Examples

You can use the following DAX switch statement to select the correct translated value in a dynamic visual title in Power BI:

```dax


SWITCH (

  USERCULTURE(),

  "de-DE", "Umsatz nach Produkt",

  "fr-FR", "Ventes par produit",

  "Sales by product"

)

```

## Related articles

Learn more about USERCULTURE in the following articles:

- [**Expression-based titles in Power BI Desktop: Create a field for your title**](https://docs.microsoft.com/en-us/power-bi/create-reports/desktop-conditional-format-visual-titles#create-a-field-for-your-title)

  You can create language-specific titles in a DAX measure by using the USERCULTURE() function. This function returns the culture code for the user, based on their operating system or browser settings. [» Read more](https://docs.microsoft.com/en-us/power-bi/create-reports/desktop-conditional-format-visual-titles#create-a-field-for-your-title)
- [**Introducing user-aware calculated columns in Power BI**](https://www.sqlbi.com/articles/introducing-user-aware-calculated-columns-in-power-bi/)

  This article describes the new Expression Context property of calculated columns in Power BI, explaining how user-aware calculated columns work, why they are not materialized, and how to use them as virtual calculated columns for localization and custom security scenarios. [» Read more](https://www.sqlbi.com/articles/introducing-user-aware-calculated-columns-in-power-bi/)

## Related functions

Other related functions are:

- [[USERNAME]]
- [[USEROBJECTID]]
- [[USERPRINCIPALNAME]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/dax/userculture-function-dax](https://learn.microsoft.com/dax/userculture-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
