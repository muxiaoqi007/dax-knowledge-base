---
title: "Specifying multiple filter conditions in CALCULATE"
url: "https://www.sqlbi.com/articles/specifying-multiple-filter-conditions-in-calculate"
published: "2021-04-24"
updated: 
source: "sqlbi.com"
---

# Specifying multiple filter conditions in CALCULATE

> 发布：2021-04-24

A new syntax was introduced in the March 2021 version of Power BI Desktop that simplifies the writing of complex filter conditions in [[CALCULATE]] functions. In short, the following measures are now valid DAX expressions:

```dax


Red or Contoso Sales :=

CALCULATE (

    [Sales Amount],

    'Product'[Color] = "Red" || 'Product'[Brand] = "Contoso"

)



Big Sales Amount :=

CALCULATE (

    [Sales Amount],

    KEEPFILTERS ( Sales[Quantity] * Sales[Net Price] > 1000 )

)

```

## How DAX had been working before

In DAX, a filter is a table. Therefore, writing a predicate in [[CALCULATE]] is just syntax sugar for a longer syntax. For example, the following measure:

```dax


Red :=

CALCULATE (

    [Sales Amount],

    'Product'[Color] = "Red"

)

```

Corresponds to the following complete syntax, where the *Product[Color]* filter is a table with the list of values allowed in the filter context:

```dax


Red Sales :=

CALCULATE (

    [Sales Amount],

    FILTER ( 

        ALL ( 'Product'[Color] ),

        'Product'[Color] = "Red"

    )

)

```

This automatic translation only supported conditions specifying a single column reference. The same column can be referenced multiple times, like in the following measure:

```dax


Red or Blue Sales :=

CALCULATE (

    [Sales Amount],

    'Product'[Color] = "Red" || 'Product'[Color] = "Blue"

)

```

Referencing multiple columns in the same predicate was not possible. In those cases, a multicolumn filter required the complete syntax, as in the following example:

```dax


Red or Contoso Sales :=

CALCULATE (

    [Sales Amount],

    FILTER (

        ALL ( 'Product'[Color], 'Product'[Brand] ),

        'Product'[Color] = "Red" || 'Product'[Brand] = "Contoso"

    )

)

```

A common error is to use a table filter instead of a multi-column filter. For example, the *Big Sales Amount* measure or the initial example is often written in this sub-optimal manner:

```dax


Big Sales Amount :=

CALCULATE (

    [Sales Amount],

    FILTER ( 

        Sales, 

        Sales[Quantity] * Sales[Net Price] > 1000

    )

)

```

Once again, the best option for this filter is to write a multi-column filter in an explicit way. The following version of *Big Sales Amount* uses [[KEEPFILTERS]] just to keep the semantics of the previous non-optimized version:

```dax


DEFINE

    MEASURE Sales[Big Sales Amount] =

        CALCULATE (

            [Sales Amount],

            KEEPFILTERS (

                FILTER (

                    ALL ( Sales[Quantity], Sales[Net Price] ),

                    Sales[Quantity] * Sales[Net Price] > 1000

                )

            )

        )

EVALUATE

SUMMARIZECOLUMNS (

    Sales[Quantity],

    "Sales Amount", [Sales Amount],

    "Big Sales Amount", [Big Sales Amount]

)

```

| Quantity | Sales Amount | Big Sales Amount |
| --- | --- | --- |
| 1 | 17,569,935.65 | 4,097,102.62 |
| 2 | 2,951,553.65 | 1,516,230.38 |
| 4 | 5,735,255.24 | 4,424,075.64 |
| 3 | 4,334,599.44 | 2,865,005.14 |

However, both the last two versions are different from the syntax described in the initial example of the article. More details about this in the next sections.

## How DAX works now

DAX now supports expressions where multiple columns belonging to the same table are part of the predicate expression in a [[CALCULATE]] filter argument.

Thus, the following *Big Sales Amount Overrides Filter* measure is now a valid DAX expression:

```dax


Big Sales Amount Overrides Filter :=

CALCULATE (

    [Sales Amount],

    Sales[Quantity] * Sales[Net Price] > 1000

)

```

Internally, this code is executed as the following expression:

```dax


Big Sales Amount Overrides Filter :=

CALCULATE (

    [Sales Amount],

    FILTER ( 

        ALL ( Sales[Quantity], Sales[Net Price] ), 

        Sales[Quantity] * Sales[Net Price] > 1000

    )

)

```

The filter overrides any existing filter on *Sales[Quantity]* and *Sales[Net Price]*. For example, a slicer with a filter on *Sales[Quantity]* would be ignored by the *Big Sales Amount Overrides Filter* measure. In order to keep the existing filter on a slicer, you can use [[KEEPFILTERS]] as in the *Big Sales Amount* measure shown at the beginning of the article:

```dax


DEFINE

    MEASURE Sales[Big Sales Amount] =

        CALCULATE (

            [Sales Amount],

            KEEPFILTERS ( Sales[Quantity] * Sales[Net Price] > 1000 )

        )

    MEASURE Sales[Big Sales Amount Overrides Filter] =

        CALCULATE ( 

            [Sales Amount], 

            Sales[Quantity] * Sales[Net Price] > 1000 

        )

EVALUATE

SUMMARIZECOLUMNS (

    Sales[Quantity],

    "Sales Amount", [Sales Amount],

    "Big Sales Amount", [Big Sales Amount],

    "Big Sales Amount Overrides Filter", [Big Sales Amount Overrides Filter]

)

```

| Quantity | Sales Amount | Big Sales Amount | Big Sales Amount Overrides Filter |
| --- | --- | --- | --- |
| 1 | 17,569,935.65 | 4,097,102.62 | 12,902,413.78 |
| 2 | 2,951,553.65 | 1,516,230.38 | 12,902,413.78 |
| 4 | 5,735,255.24 | 4,424,075.64 | 12,902,413.78 |
| 3 | 4,334,599.44 | 2,865,005.14 | 12,902,413.78 |

The columns specified in one same predicate must belong to the same table. The following expression is therefore still invalid in DAX:

```dax


Sales Multiple or Red :=

CALCULATE (

    [Sales Amount],

    Sales[Quantity] > 1 || 'Product'[Color] = "Red"

)

```

In this last case the predicate requires a [[CROSSJOIN]] or other techniques, to reduce the cardinality if there are too many values resulting from the combinations of the columns:

```dax


DEFINE

    MEASURE Sales[Sales Multiple or Red] =

        CALCULATE (

            [Sales Amount],

            FILTER (

                CROSSJOIN ( ALL ( Sales[Quantity] ), ALL ( 'Product'[Color] ) ),

                Sales[Quantity] > 1 || 'Product'[Color] = "Red"

            )

        )

EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Brand],

    "Sales Amount", [Sales Amount],

    "Sales Multiple or Red", [Sales Multiple or Red]

)



```

| Brand | Sales Amount | Sales Multiple or Red |
| --- | --- | --- |
| Contoso | 7,352,399.03 | 3,479,904.17 |
| Wide World Importers | 1,901,956.66 | 837,419.75 |
| Northwind Traders | 1,040,552.13 | 409,048.42 |
| Adventure Works | 4,011,112.28 | 1,778,525.69 |
| Southridge Video | 1,384,413.85 | 613,492.27 |
| Litware | 3,255,704.03 | 1,447,913.33 |
| Fabrikam | 5,554,015.73 | 2,422,741.32 |
| Proseware | 2,546,144.16 | 1,139,585.31 |
| A. Datum | 2,096,184.64 | 906,209.66 |
| The Phone Company | 1,123,819.07 | 479,841.37 |
| Tailspin Toys | 325,042.42 | 142,069.31 |

## No changes in best practices

The new syntax does not change any of the best practices, but it should help in applying at least the “filter columns, don’t filter tables” rule.

Also, conditions between columns should be expressed as separate predicates. The following measure:

```dax


Red Contoso Sales Bad Practice :=

CALCULATE (

    [Sales Amount],

    'Product'[Color] = "Red" && 'Product'[Brand] = "Contoso"

)

```

Should be written as:

```dax


Red Contoso Sales :=

CALCULATE (

    [Sales Amount],

    'Product'[Color] = "Red",

    'Product'[Brand] = "Contoso"

)

```

| Sales Amount | Red Contoso Sales – Slower | Red Contoso Sales – Optimized |
| --- | --- | --- |
| 30,591,343.98 | 579,062.70 | 579,062.70 |

Multiple columns in the same predicate should be used only when necessary. A filter predicate with a simple [[AND]] condition between two columns works faster if replaced by two filter arguments, one for each column.

## Conclusions

The ability to create [[CALCULATE]] filter arguments with multiple columns simplifies the DAX code and usually provides better performance. Indeed, it generates code that is compliant with the best practices for better performance.

If you wrote multi-column predicates using [[FILTER]] over a table instead of filtering just the required columns, keep in mind that you need [[KEEPFILTERS]] in order to keep the same semantics in case you replace a [[FILTER]] over a table with the new simplified syntax.

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[KEEPFILTERS]]

CALCULATE modifier

Changes the CALCULATE and CALCULATETABLE function filtering semantics.

`KEEPFILTERS ( <Expression> )`

[[CROSSJOIN]]

Returns a table that is a crossjoin of the specified tables.

`CROSSJOIN ( <Table> [, <Table> [, … ] ] )`

[[AND]]

Checks whether all arguments are TRUE, and returns TRUE if all arguments are TRUE.

`AND ( <Logical1>, <Logical2> )`

[[FILTER]]

Returns a table that has been filtered.

`FILTER ( <Table>, <FilterExpression> )`
