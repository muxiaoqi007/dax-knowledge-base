---
title: "EXTERNALMEASURE"
function: "externalmeasure"
url: "https://dax.guide/externalmeasure/"
source: "dax.guide"
重要度:
难度:
---

# EXTERNALMEASURE DAX Function

Invoke a measure defined in a remote model.

## Syntax

EXTERNALMEASURE ( <MeasureName>, <DataType>, <Connection> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| MeasureName |  | Name of the measure as defined in the remote model. |
| Type | Optional | A type name to be associated with the measure: BOOLEAN/LOGICAL, CURRENCY/DECIMAL, DATETIME, DOUBLE, INTEGER/INT64, STRING/TEXT, VARIANT. |
| Datasource | Optional | The name of external data source executing the measure. |

## Return values

Scalar A single value of any type.

Result of the remote measure with the data type specified by the DataType parameter.

## Remarks

A composite model defines a local version of the measures that reference measures in remote models by using EXTERNALMEASURE. Power BI automatically defines these local measures when a composite model is created or updated.

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber, Ming Han Teh

Microsoft documentation: [https://learn.microsoft.com/en-us/dax/externalmeasure-function-dax](https://learn.microsoft.com/en-us/dax/externalmeasure-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
