---
title: "IGNORE"
function: "ignore"
category: "Table manipulation"
url: "https://dax.guide/ignore/"
source: "dax.guide"
重要度:
难度:
---

# IGNORE DAX Function (Table manipulation)

Tags a measure expression specified in the call to [[SUMMARIZECOLUMNS]] function to be ignored when determining the non-blank rows.

## Syntax

IGNORE ( <Measure\_Expression> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Measure\_Expression |  | The Expression which needs to be ignored for the purposes of determining non-blank rows. |

## Return values

This function does not return a value.

## Remarks

The IGNORE() syntax can be used to modify the behavior of the [[SUMMARIZECOLUMNS]] function by omitting specific expressions from the BLANK/NULL evaluation. Rows for which all expressions not using IGNORE return BLANK/NULL will be excluded independent of whether the expressions which do use IGNORE evaluate to BLANK/NULL or not.

[» 4 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  IGNORE tags a measure in SUMMARIZECOLUMNS to be ignored when

--  determining the non-blank rows.

EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Calendar Year],

    "Amount", [Sales Amount]

)



EVALUATE

SUMMARIZECOLUMNS (

    'Date'[Calendar Year],

    "Amount", IGNORE ( [Sales Amount] )

)

```

| Calendar Year | Amount |
| --- | --- |
| 2007-01-01 | 11,309,946.12 |
| 2008-01-01 | 9,927,582.99 |
| 2009-01-01 | 9,353,814.87 |

| Calendar Year | Amount |
| --- | --- |
| 2005-01-01 | (Blank) |
| 2006-01-01 | (Blank) |
| 2007-01-01 | 11,309,946.12 |
| 2008-01-01 | 9,927,582.99 |
| 2009-01-01 | 9,353,814.87 |
| 2010-01-01 | (Blank) |
| 2011-01-01 | (Blank) |

## Related articles

Learn more about IGNORE in the following articles:

- [**Introducing SUMMARIZECOLUMNS**](https://www.sqlbi.com/articles/introducing-summarizecolumns/)

  This article explains how to use SUMMARIZECOLUMNS, which is a replacement of SUMMARIZE and does not require the use of ADDCOLUMNS to obtain good performance. [» Read more](https://www.sqlbi.com/articles/introducing-summarizecolumns/)
- [**The importance of star schemas in Power BI**](https://www.sqlbi.com/articles/the-importance-of-star-schemas-in-power-bi/)

  Creating a star schema in Power BI is the best practice to improve performance and more importantly, to ensure accurate results! This article shows why a star schema can fix some of the issues in your report. [» Read more](https://www.sqlbi.com/articles/the-importance-of-star-schemas-in-power-bi/)
- [**Using field parameters and calculation groups for conditional formatting**](https://www.sqlbi.com/articles/using-field-parameters-and-calculation-groups-for-conditional-formatting/)

  This article describes how to apply conditional formatting on measures picked from a slicer and implemented using two techniques: field parameters and calculation groups. [» Read more](https://www.sqlbi.com/articles/using-field-parameters-and-calculation-groups-for-conditional-formatting/)
- [**SUMMARIZECOLUMNS best practices**](https://www.sqlbi.com/articles/summarizecolumns-best-practices/)

  SUMMARIZECOLUMNS is a powerful and complex function in DAX that in 2025 can be used in measures. This article outlines the best practices when using this function to avoid incorrect results. [» Read more](https://www.sqlbi.com/articles/summarizecolumns-best-practices/)

## Related functions

Other related functions are:

- [[SUMMARIZECOLUMNS]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/summarizecolumns-function-dax](https://docs.microsoft.com/en-us/dax/summarizecolumns-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
