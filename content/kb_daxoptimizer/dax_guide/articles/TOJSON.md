---
title: "TOJSON"
function: "tojson"
category: "Text"
url: "https://dax.guide/tojson/"
source: "dax.guide"
重要度:
难度:
---

# TOJSON DAX Function (Text)

Converts the records of a table into a JSON text.

## Syntax

TOJSON ( <Table> [, <MaxRows>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table |  | The table to be converted. |
| MaxRows | Optional | The maximum number of rows to be converted. A negative number means all rows are converted. Default is 10. |

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

Result in JSON format.

## Remarks

The sort order of the result cannot be controlled.

[» 1 related article](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


DEFINE MEASURE Sales[ByCountry] =

	VAR SalesByCustomer = 

	    ADDCOLUMNS ( 

			VALUES ( Customer[CountryRegion] ), 

			"Amount", [Sales Amount] 

		)

	VAR Result = TOJSON (  SalesByCustomer, 3  )

	RETURN Result

EVALUATE

SUMMARIZECOLUMNS ( 'Customer'[Continent], "By Country JSON", [ByCountry] )

```

| Continent | By Country JSON |
| --- | --- |
| Asia | {  “header”: [“‘Customer'[CountryRegion]”, “[Amount]”],  “rowCount”: 15,  “data”: [  [“Australia”, 7638059.9358],  [“Turkmenistan”, 118336.5519],  [“Thailand”, 63107.118]  ]  } |
| North America | {  “header”: [“‘Customer'[CountryRegion]”, “[Amount]”],  “rowCount”: 2,  “data”: [  [“United States”, 10312118.2484],  [“Canada”, 885208.0705]  ]  } |
| Europe | {  “header”: [“‘Customer'[CountryRegion]”, “[Amount]”],  “rowCount”: 12,  “data”: [  [“Germany”, 2519890.7983],  [“United Kingdom”, 3621032.1587],  [“France”, 1109665.4323]  ]  } |

## Related articles

Learn more about TOJSON in the following articles:

- [**Debugging DAX variables using TOJSON and TOCSV**](https://www.sqlbi.com/articles/debugging-dax-variables-using-tojson-and-tocsv/)

  This article describes how to use the TOJSON and TOCSV functions to inspect the content of intermediate table variables when debugging a DAX measure. [» Read more](https://www.sqlbi.com/articles/debugging-dax-variables-using-tojson-and-tocsv/)

## Related functions

Other related functions are:

- [[TOCSV]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://learn.microsoft.com/dax/tojson-function-dax](https://learn.microsoft.com/dax/tojson-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
