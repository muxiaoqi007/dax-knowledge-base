---
title: "MINA"
function: "mina"
category: "Aggregation"
url: "https://dax.guide/mina/"
source: "dax.guide"
重要度:
难度:
---

# MINA DAX Function (Aggregation)

Returns the smallest value in a column. Does not ignore logical values and text.

## Syntax

MINA ( <ColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName |  | The column for which you want to find the smallest value. |

## Return values

Scalar A single value of any type.

Smallest value found in the column.

## Remarks

The function MINA corresponds to [[MIN]] used with a single column as an argument for all the data types except Boolean.  
When MINA operates with a Boolean data type, it consider TRUE as 1 and FALSE as 0.

For arguments that are not Boolean, the MINA function internally executes [[MINX]], without any performance difference.  
The following MINA call:

```dax


MINA ( table[column] )

```

corresponds to the following [[MINX]] call:

```dax


MINX (

    table,

    table[column] 

)

```

The result is blank in case there are no rows in the table with a non-blank value.

The behavior described above has an undocumented side effect caused by a bug that could be fixed in 2025 or later: if a measure is passed as an argument, it is evaluated by iterating the table where the measure is defined. We recommend not using this behavior, as it is misleading and breaks a calculation if the measure is moved to another table just to reorganize the presentation layer – it will also change once Microsoft fixes the bug.

[» 2 related functions](#alt)  

## Examples

More examples available for the MIN and MINX functions.

```dax


--  MINA required to aggregate boolean columns

--      FALSE is 0

--      TRUE  is 1

DEFINE

    MEASURE Customer[Group No Cars 1] = MINA ( Customer[NoCars] )

    MEASURE Customer[Group No Cars 2] = MINX ( Customer, 1 * Customer[NoCars] )

EVALUATE

SUMMARIZECOLUMNS ( 

    Customer[State], 

    "Group has no cars 1", [Group No Cars 1],

    "Group has no cars 2", [Group No Cars 2] 

)

ORDER BY customer[State]

```

| State | Group has no cars 1 | Group has no cars 2 |
| --- | --- | --- |
| Ahal Province | 1 | 1 |
| Alabama | 1 | 1 |
| Alaska | 1 | 1 |
| Alberta | 0 | 0 |
| Alpes-Maritimes | 1 | 1 |
| Arizona | 0 | 0 |
| Armenia | 1 | 1 |
| Baden-Wuerttemberg | 0 | 0 |
| Bavaria | 0 | 0 |
| Beijing | 1 | 1 |
| Berlin | 0 | 0 |
| Bern | 1 | 1 |
| British Columbia | 0 | 0 |
| California | 0 | 0 |
| Central Greece and Evvoia | 1 | 1 |
| Chubu | 1 | 1 |
| Chuy Province | 1 | 1 |
| Colorado | 1 | 1 |
| Connecticut | 1 | 1 |
| Damascus | 1 | 1 |
| England | 0 | 0 |
| Essonne | 0 | 0 |
| Florida | 0 | 0 |
| Garonne (Haute) | 0 | 0 |
| Georgia | 0 | 0 |
| GuangDong | 1 | 1 |
| Hamburg | 0 | 0 |
| Hauts de Seine | 0 | 0 |
| Hesse | 0 | 0 |
| Hokkaido | 1 | 1 |
| Hong Kong | 1 | 1 |
| Illinois | 0 | 0 |
| Islamabad Capital Territory | 1 | 1 |
| Kansai | 1 | 1 |
| Kanto | 1 | 1 |
| Kentucky | 1 | 1 |
| Krung Thep | 1 | 1 |
| Leinster | 1 | 1 |
| Lisboa Region | 1 | 1 |
| Loiret | 0 | 0 |
| Lombardia | 1 | 1 |
| Lower Saxony | 0 | 0 |
| Madrid | 1 | 1 |
| Maharashtra | 1 | 1 |
| Maine | 1 | 1 |
| Maryland | 1 | 1 |
| Massachusetts | 0 | 0 |
| Minnesota | 1 | 1 |
| Mississippi | 0 | 0 |
| Missouri | 0 | 0 |
| Molonglo | 1 | 1 |
| Montana | 0 | 0 |
| Moselle | 0 | 0 |
| Moskovskaya oblast | 1 | 1 |
| National Capital Territory of Delhi | 1 | 1 |
| New Jersey | 1 | 1 |
| New South Wales | 0 | 0 |
| New York | 0 | 0 |
| Noord-Holland | 1 | 1 |
| Nord | 0 | 0 |
| North Carolina | 0 | 0 |
| North Rhine-Westphalia | 0 | 0 |
| Northwestern | 1 | 1 |
| Ohio | 0 | 0 |
| Ontario | 0 | 0 |
| Oregon | 0 | 0 |
| Pas de Calais | 0 | 0 |
| Queensland | 0 | 0 |
| Saar | 0 | 0 |
| Saxony | 0 | 0 |
| Schleswig-Holstein | 0 | 0 |
| Scotland | 1 | 1 |
| Seine (Paris) | 0 | 0 |
| Seine et Marne | 0 | 0 |
| Seine Saint Denis | 0 | 0 |
| Seoul-jikhalsi | 1 | 1 |
| Shanghai | 1 | 1 |
| Singapore | 1 | 1 |
| Somme | 0 | 0 |
| South Australia | 0 | 0 |
| South Carolina | 0 | 0 |
| Taiwan | 1 | 1 |
| Tasmania | 0 | 0 |
| Tehran | 1 | 1 |
| Texas | 0 | 0 |
| Thimphu District | 1 | 1 |
| Tohoku | 1 | 1 |
| Utah | 0 | 0 |
| Val de Marne | 0 | 0 |
| Val d’Oise | 0 | 0 |
| Victoria | 0 | 0 |
| Virginia | 0 | 0 |
| Warszawa | 1 | 1 |
| Washington | 0 | 0 |
| West Bengal | 1 | 1 |
| Wisconsin | 1 | 1 |
| Wyoming | 1 | 1 |
| Xinjiang | 1 | 1 |
| Yeongnam | 1 | 1 |
| Yveline | 0 | 0 |

## Related functions

Other related functions are:

- [[MIN]]
- [[MINX]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Daniil Maslyuk

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/mina-function-dax](https://docs.microsoft.com/en-us/dax/mina-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
