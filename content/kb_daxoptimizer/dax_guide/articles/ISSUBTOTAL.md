---
title: "ISSUBTOTAL"
function: "issubtotal"
category: "Information"
url: "https://dax.guide/issubtotal/"
source: "dax.guide"
重要度:
难度:
---

# ISSUBTOTAL DAX Function (Information) Not recommended

Returns TRUE if the current row contains a subtotal for a specified column and FALSE otherwise.

## Syntax

ISSUBTOTAL ( <ColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName |  | The column you want to test. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

Returns TRUE if the current row contains a subtotal for a specified column and FALSE otherwise.

## Remarks

The ISSUBTOTAL function is used exclusively within [[SUMMARIZE]].  
It is suggested to use [[SUMMARIZECOLUMNS]] instead of [[SUMMARIZE]] to create reports with subtotals.

[» 1 related function](#alt)  

## Examples

```dax


--  ISSUBTOTAL checks if the current row in SUMMARIZE is 

--  a subtotal for a column used in a ROLLUP

--

--  It is superseded by SUMMARIZECOLUMNS that has the 

--  option of ROLLUPADDISSUBTOTAL, combining both ROLLUP

--  and ISSUBTOTAL in the same function.

EVALUATE

CALCULATETABLE (

    SUMMARIZE (

        'Product',

        ROLLUP ( 'Product'[Brand] ),

        "IsSubtotal", ISSUBTOTAL ( 'Product'[Brand] ),

        "Sales", [Sales Amount]

    ),

    Product[Brand] IN { "Contoso", "A. Datum", "Proseware" }

)

```

| Brand | IsSubtotal | Sales |
| --- | --- | --- |
| Contoso | false | 7,352,399.03 |
| A. Datum | false | 2,096,184.64 |
| Proseware | false | 2,546,144.16 |
| (Blank) | true | 11,994,727.82 |

## Related functions

Other related functions are:

- [[SUMMARIZE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/issubtotal-function-dax](https://docs.microsoft.com/en-us/dax/issubtotal-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
