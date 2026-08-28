---
title: "MAXA"
function: "maxa"
category: "Aggregation"
url: "https://dax.guide/maxa/"
source: "dax.guide"
重要度:
难度:
---

# MAXA DAX Function (Aggregation)

Returns the largest value in a column. Does not ignore logical values and text.

## Syntax

MAXA ( <ColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName |  | The column in which you want to find the largest value. |

## Return values

Scalar A single value of any type.

Largest value found in the column.

## Remarks

The function MAXA corresponds to [[MAX]] used with a single column as an argument for all the data types except Boolean.  
When MAXA operates with a Boolean data type, it consider TRUE as 1 and FALSE as 0.

For arguments that are not Boolean, the MAXA function internally executes [[MAXX]], without any performance difference.  
The following MAXA call:

```dax


MAXA ( table[column] )

```

corresponds to the following [[MAXX]] call:

```dax


MAXX (

    table,

    table[column] 

)

```

The result is blank in case there are no rows in the table with a non-blank value.

The behavior described above has an undocumented side effect caused by a bug that could be fixed in 2025 or later: if a measure is passed as an argument, it is evaluated by iterating the table where the measure is defined. We recommend not using this behavior, as it is misleading and breaks a calculation if the measure is moved to another table just to reorganize the presentation layer – it will also change once Microsoft fixes the bug.

[» 2 related functions](#alt)  

## Examples

More examples available for the MAX and MAXX functions.

```dax


--  MAXA required to aggregate boolean columns

--      FALSE is 0

--      TRUE  is 1

DEFINE

    MEASURE Customer[Group Large Family 1] = MAXA ( Customer[LargeFamily] )

    MEASURE Customer[Group Large Family 2] = 

        MAXX ( Customer, 1 * Customer[LargeFamily] )

EVALUATE

SUMMARIZECOLUMNS ( 

    Customer[State], 

    "State has large families 1", [Group Large Family 1],

    "State has large families 2", [Group Large Family 2] 

)

ORDER BY customer[State]

```

| State | State has large families 1 | State has large families 2 |
| --- | --- | --- |
| Ahal Province | 0 | 0 |
| Alabama | 0 | 0 |
| Alaska | 0 | 0 |
| Alberta | 1 | 1 |
| Alpes-Maritimes | 0 | 0 |
| Arizona | 0 | 0 |
| Armenia | 0 | 0 |
| Baden-Wuerttemberg | 1 | 1 |
| Bavaria | 1 | 1 |
| Beijing | 0 | 0 |
| Berlin | 1 | 1 |
| Bern | 0 | 0 |
| British Columbia | 1 | 1 |
| California | 1 | 1 |
| Central Greece and Evvoia | 0 | 0 |
| Chubu | 0 | 0 |
| Chuy Province | 0 | 0 |
| Colorado | 0 | 0 |
| Connecticut | 0 | 0 |
| Damascus | 0 | 0 |
| England | 1 | 1 |
| Essonne | 1 | 1 |
| Florida | 1 | 1 |
| Garonne (Haute) | 1 | 1 |
| Georgia | 0 | 0 |
| GuangDong | 0 | 0 |
| Hamburg | 1 | 1 |
| Hauts de Seine | 1 | 1 |
| Hesse | 1 | 1 |
| Hokkaido | 0 | 0 |
| Hong Kong | 0 | 0 |
| Illinois | 0 | 0 |
| Islamabad Capital Territory | 0 | 0 |
| Kansai | 0 | 0 |
| Kanto | 0 | 0 |
| Kentucky | 0 | 0 |
| Krung Thep | 0 | 0 |
| Leinster | 0 | 0 |
| Lisboa Region | 0 | 0 |
| Loiret | 1 | 1 |
| Lombardia | 0 | 0 |
| Lower Saxony | 1 | 1 |
| Madrid | 0 | 0 |
| Maharashtra | 0 | 0 |
| Maine | 0 | 0 |
| Maryland | 0 | 0 |
| Massachusetts | 0 | 0 |
| Minnesota | 0 | 0 |
| Mississippi | 0 | 0 |
| Missouri | 1 | 1 |
| Molonglo | 0 | 0 |
| Montana | 1 | 1 |
| Moselle | 1 | 1 |
| Moskovskaya oblast | 0 | 0 |
| National Capital Territory of Delhi | 0 | 0 |
| New Jersey | 0 | 0 |
| New South Wales | 1 | 1 |
| New York | 0 | 0 |
| Noord-Holland | 0 | 0 |
| Nord | 1 | 1 |
| North Carolina | 0 | 0 |
| North Rhine-Westphalia | 1 | 1 |
| Northwestern | 0 | 0 |
| Ohio | 0 | 0 |
| Ontario | 0 | 0 |
| Oregon | 1 | 1 |
| Pas de Calais | 1 | 1 |
| Queensland | 1 | 1 |
| Saar | 1 | 1 |
| Saxony | 1 | 1 |
| Schleswig-Holstein | 1 | 1 |
| Scotland | 0 | 0 |
| Seine (Paris) | 1 | 1 |
| Seine et Marne | 1 | 1 |
| Seine Saint Denis | 1 | 1 |
| Seoul-jikhalsi | 0 | 0 |
| Shanghai | 0 | 0 |
| Singapore | 0 | 0 |
| Somme | 1 | 1 |
| South Australia | 1 | 1 |
| South Carolina | 0 | 0 |
| Taiwan | 0 | 0 |
| Tasmania | 1 | 1 |
| Tehran | 0 | 0 |
| Texas | 1 | 1 |
| Thimphu District | 0 | 0 |
| Tohoku | 0 | 0 |
| Utah | 0 | 0 |
| Val de Marne | 1 | 1 |
| Val d’Oise | 1 | 1 |
| Victoria | 1 | 1 |
| Virginia | 1 | 1 |
| Warszawa | 0 | 0 |
| Washington | 1 | 1 |
| West Bengal | 0 | 0 |
| Wisconsin | 0 | 0 |
| Wyoming | 1 | 1 |
| Xinjiang | 0 | 0 |
| Yeongnam | 0 | 0 |
| Yveline | 1 | 1 |

## Related functions

Other related functions are:

- [[MAX]]
- [[MAXX]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber, Daniil Maslyuk

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/maxa-function-dax](https://docs.microsoft.com/en-us/dax/maxa-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
