---
title: "ROLLUPISSUBTOTAL"
function: "rollupissubtotal"
category: "Table manipulation"
url: "https://dax.guide/rollupissubtotal/"
source: "dax.guide"
重要度:
难度:
---

# ROLLUPISSUBTOTAL DAX Function (Table manipulation)

Pairs up the rollup groups with the column added by [[ROLLUPADDISSUBTOTAL]].

## Syntax

ROLLUPISSUBTOTAL ( [<GrandtotalFilter>], <GroupBy\_ColumnName>, <IsSubtotal\_ColumnName> [, [<GroupLevelFilter>] [, <GroupBy\_ColumnName>, <IsSubtotal\_ColumnName> [, [<GroupLevelFilter>] [, … ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| GrandtotalFilter | Optional | Filter to be applied to the grandtotal level. |
| GroupBy\_ColumnName | Repeatable | A column to be returned. |
| IsSubtotal\_ColumnName | Repeatable | An added IsSubtotal column. |
| GroupLevelFilter | Optional Repeatable | Filter to be applied to the current level. |

## Return values

The function does not return a value. It only marks a subset of columns to [[ADDMISSINGITEMS]].

## Remarks

The ROLLUPISSUBTOTAL function is used exclusively within [[ADDMISSINGITEMS]].

[» 2 related functions](#alt)  

## Examples

```dax


--  ROLLUPISSUBTOTAL populates in ADDMISSINGITEMS the corresponding columns

--  created by ROLLUPADDISSUBTOTAL in SUMMARIZECOLUMNS.

EVALUATE

ADDMISSINGITEMS (

    'Date'[Calendar Year],

    'Date'[Calendar Year Month],

    SUMMARIZECOLUMNS (

        ROLLUPADDISSUBTOTAL (

            'Date'[Calendar Year],

            "IsYearTotal",

            'Date'[Calendar Year Month],

            "IsYearMonthTotal"

        ),

        "Amt", [Sales Amount]

    ),

    ROLLUPISSUBTOTAL (

        'Date'[Calendar Year],

        [IsYearTotal],

        'Date'[Calendar Year Month],

        [IsYearMonthTotal]

    )

)

```

## Related functions

Other related functions are:

- [[ADDMISSINGITEMS]]
- [[ROLLUPGROUP]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Imke Feldmann

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/rollupissubtotal-function-dax](https://docs.microsoft.com/en-us/dax/rollupissubtotal-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
