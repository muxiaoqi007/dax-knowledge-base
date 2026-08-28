---
title: "TOCSV"
function: "tocsv"
category: "Text"
url: "https://dax.guide/tocsv/"
source: "dax.guide"
重要度:
难度:
---

# TOCSV DAX Function (Text)

Converts the records of a table into a CSV (comma-separated values) text.

## Syntax

TOCSV ( <Table> [, <MaxRows>] [, <Delimiter>] [, <IncludeHeaders>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table |  | The table to be converted. |
| MaxRows | Optional | The maximum number of rows to be converted. A negative number means all rows are converted. Default is 10. |
| Delimiter | Optional | The field separator. Must be a non-empty constant string. Default is ‘,’. |
| IncludeHeaders | Optional | If true, the header row is included. Default is true. |

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

Result in CSV format.

## Remarks

The sort order of the result cannot be controlled.  
The new line character cannot be modified.  
Any quotation marks (") within the value are replaced with 2 quotation marks (""). If the original value contains a quotation mark or the delimiter, then the entire value, after the aforementioned replacement of quotation marks, is enclosed in quotation marks. The delimiter is never affected by the addition or replacement of quotation marks, even if the delimiter contains a quotation mark.  
In case of need, use [[CONCATENATEX]] to create a CSV controlling the order and the new line character.

[» 1 related article](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


DEFINE MEASURE Sales[ByCountry] =

	VAR SalesByCustomer = 

	    ADDCOLUMNS ( 

			VALUES ( Customer[CountryRegion] ), 

			"Amount", [Sales Amount] 

		)

	VAR Result = TOCSV (  SalesByCustomer, 3, ",", FALSE  )

	RETURN Result

EVALUATE

SUMMARIZECOLUMNS ( 'Customer'[Continent], "By Country CSV", [ByCountry] )

```

| Continent | By Country CSV |
| --- | --- |
| Asia | Australia,7638059.93580003  Turkmenistan,118336.552  Thailand,63107.118 |
| North America | United States,10312118.2484997  Canada,885208.0705 |
| Europe | Germany,2519890.79830002  United Kingdom,3621032.15870003  France,1109665.43230001 |

## Related articles

Learn more about TOCSV in the following articles:

- [**Debugging DAX variables using TOJSON and TOCSV**](https://www.sqlbi.com/articles/debugging-dax-variables-using-tojson-and-tocsv/)

  This article describes how to use the TOJSON and TOCSV functions to inspect the content of intermediate table variables when debugging a DAX measure. [» Read more](https://www.sqlbi.com/articles/debugging-dax-variables-using-tojson-and-tocsv/)

## Related functions

Other related functions are:

- [[TOJSON]]
- [[CONCATENATEX]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://learn.microsoft.com/dax/tocsv-function-dax](https://learn.microsoft.com/dax/tocsv-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
