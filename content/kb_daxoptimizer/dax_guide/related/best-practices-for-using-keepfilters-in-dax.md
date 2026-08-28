---
title: "Best practices for using KEEPFILTERS in DAX"
url: "https://www.sqlbi.com/articles/best-practices-for-using-keepfilters-in-dax"
published: "2024-05-07"
updated: 
source: "sqlbi.com"
---

# Best practices for using KEEPFILTERS in DAX

> 发布：2024-05-07

In the article [Using KEEPFILTERS in DAX](https://www.sqlbi.com/articles/using-keepfilters-in-dax-updated/), we described how to use [[KEEPFILTERS]], which is a function that preserves the existing filter on columns affected by a new filter applied by [[CALCULATE]] or [[CALCULATETABLE]]. We suggest reading that article if you are not familiar with [[KEEPFILTERS]]. However, we wanted to clarify a rule of thumb you can apply to quickly decide when to use [[KEEPFILTERS]] or not in most cases. This will allow you to only invest more time when you are dealing with specific requirements.

## Common use cases

[[KEEPFILTERS]] is a filter modifier that should be evaluated on a case-by-case basis to ensure that its use corresponds to the specific requirements of the calculation. However, we can describe a few common cases where we can say with 95% certainty whether [[KEEPFILTERS]] is used or not:

- When you filter one value in one column, [[KEEPFILTERS]] is commonly not used.
- When you filter two or more values in one column, [[KEEPFILTERS]] is commonly used.
- When you filter two or more columns, [[KEEPFILTERS]] is commonly used.

The rationale is that whenever you filter one value, you probably want to override any existing filter on the same column. Indeed, otherwise, the combination between the existing filter and the new filter could return a blank value. Whenever you filter two or more values in one column, you probably want to actually filter those values that exist in both filters – the new filter and the existing filter.

Clearly, there are exceptions. Sometimes, you do not want to apply the common use case because it is part of the requirements. However, the principle is that you should be able to justify the exceptions, not the rule. In other words, you must be able to comment on the code and explain why you are deviating from the common case. This will help future you and others maintain the code as well as understand that the choice was intentional and motivated, as opposed to it being a mistake.

Let’s see these use cases in more detail.

## Do not use [[KEEPFILTERS]] for single-value filters in one column

A common case of a single-value filter is a calculation used as a denominator in a KPI. For example, consider the following *Color KPI* measure, which compares the *Sales Amount* of selected colors over the sales of Red products:

Measure in Sales table

```dax


Color KPI =

DIVIDE (

    [Sales Amount],

    CALCULATE (

        [Sales Amount],

        'Product'[Color] = "Red"

    ) 

) 

```

For example, the sales of Orange and Pink products are around half those of the Red products, whereas the Black products generate more than eight times the revenues obtained with the Red products.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-82.png)

If [[KEEPFILTERS]] were used in this case, all the rows other than Red would display a blank result; for example, in the first row, the filter would look for products that are simultaneously Azure and Red, but a product can have only one color!

An exception to this case could be when the single value selected should be ignored (thus producing a blank result) when the user selection does not include the value to filter. This is an unlikely case for a single value selection, and we do not have a practical example of this. Look at the following examples for the filters with multiple values or columns, to figure out the scenarios when the user selection should be considered in the calculation.

## Use [[KEEPFILTERS]] to filter two or more values in one column

Whenever a filter on one column might have more than one value, the existing filter should be preserved to avoid the user selection being ignored and overridden by the new filter. It is important to consider that any predicate other than = and == can produce a list of values! Operators like IN, <, >, <=, >=, and <> commonly return multiple values, and as such, they should be surrounded by [[KEEPFILTERS]].

The first example is the *% Trendy Colors* measure, which displays the percentage of sales of products that are a trendy color (Blue, Red, or White). The [[KEEPFILTERS]] is required to intersect this list of colors with any existing selection on the report:

Measure in Sales table

```dax


% Trendy Colors = 

DIVIDE (

    CALCULATE ( 

        [Sales Amount],

        KEEPFILTERS ( 'Product'[Color] IN { "Blue", "Red", "White" } )

    ),

    [Sales Amount]

)

```

The result shows the importance of the trendy colors for each product brand.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-79.png)

The *% Trendy Colors* measure is always less than or equal to 100% because it represents a subset of available colors. However, the previous report has no filter on *Product[Color]*. To understand the importance of [[KEEPFILTERS]], we should consider a report in which the user selects an arbitrary set of colors. The *Wrong % Trendy Colors* measure has no [[KEEPFILTERS]] in the following report.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-71.png)

If the user selects Azure, Black, Blue, and Brown, the expected result is the subset of products with a trendy color within the selected colors – exactly the result produced by the *% Trendy Colors* measure. The *Wrong % Trendy Colors* does not preserve the user selection, so it always compares all the trendy colors (Blue, Red, and White) with the colors selected by the user, resulting in a percentage that could be greater than 100% – all the percentages are wrong, but the numbers above 100% are clearly perceived as an error if the user is not able to evaluate the other percentages.

As a second example, consider the following *Expensive Sales* measure that returns the revenues for products that have a *Net Price* greater than 500. Because the filter on *Net Price* creates a list of values (all the existing prices greater than 500), [[KEEPFILTERS]] is required:

Measure in Sales table

```dax


Expensive Sales = 

CALCULATE (

    [Sales Amount],

    KEEPFILTERS ( Sales[Net Price] > 500 )

)

```

The importance of [[KEEPFILTERS]] is visible in the following report, where the user filters only the products with a *Net Price* of less than 1,000.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-65.png)

The *Expensive Sales* measure with [[KEEPFILTERS]] returns the revenues of products with a price between 500 and 1,000, which must be less than or equal to *Sales Amount* in the same report. The *Wrong Expensive Sales* measure does not have [[KEEPFILTERS]] and shows all the products with a price greater than 500, ignoring the *Net Price* filter applied by the user (price less than 1,000). The result could be larger than the value of *Sales Amount* in the same report, as in the highlighted cells.

## Use [[KEEPFILTERS]] to filter two or more columns

Most of the time, a filter over two or more columns includes multiple values, because otherwise it could be described by using a single filter on single columns. Therefore, it is easier to generalize the rule of thumb to specifically include multiple columns, even though it would not be strictly necessary as it is included in the previous case.

The first example shows a *Sales New Year 2018* measure that returns the revenues for December 2018 and January 2019:

Measure in Sales table

```dax


Sales New Year 2018 = 

CALCULATE ( 

    [Sales Amount],

    KEEPFILTERS ( 

        ( 'Date'[Year], 'Date'[Month] ) 

            IN { ( 2018, "December" ), ( 2019, "January" ) }   

    )

)

```

The presence of [[KEEPFILTERS]] guarantees that the user selection is preserved. The following report compares two versions of the same measure, with and without [[KEEPFILTERS]] (the latter being *Wrong Sales New Year 2018*).

![](https://cdn.sqlbi.com/wp-content/uploads/image5-56.png)

The measure without [[KEEPFILTERS]] always returns the total of the two months irrespective of the year filtered in the rows. However, there might be cases where this behavior is required – that would be the case for an exception to the rule.

Another example is a filter where we compare the product of two columns with a fixed value:

Measure in Sales table

```dax


Sales 32 = 

CALCULATE (

    [Sales Amount],

    KEEPFILTERS ( Sales[Quantity] * Sales[Net Price] = 32 )

)

```

You should not be confused by the operator: multiple combinations of Quantity and Net Price could return 32! Indeed, if the user filters *Net Price* up to 20.00, the sales of Tailspin Toys – which has a *Net Price* of 32.00 – are ignored in *Sales 32*, whereas they are included in the *Wrong Sales 32* measure that does not have [[KEEPFILTERS]].

![](https://cdn.sqlbi.com/wp-content/uploads/image6-49.png)

A more common example is a mathematical operation over two or more columns that is compared using a less than or greater than operator. However, that would have been a case similar to the one described in the multiple values for one column; we wanted to show a particular case with “equal to” because it should not be confused with the filter for one value. The presence of the “=” operator does not require [[KEEPFILTERS]] only when the comparison is for a single column!

## Conclusions

The only case where [[KEEPFILTERS]] should not be applied to a filter is when the predicate filters a single value in a single column. In all other cases—two or more values, two or more columns—you should apply [[KEEPFILTERS]] unless you can describe a specific exception to the rule. The article only covers column filters; you should always filter columns, not tables. If you have table filters, you should carefully consider whether [[KEEPFILTERS]] should be applied on a case-by-case basis.

[[KEEPFILTERS]]

CALCULATE modifier

Changes the CALCULATE and CALCULATETABLE function filtering semantics.

`KEEPFILTERS ( <Expression> )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[CALCULATETABLE]]

Context transition

Evaluates a table expression in a context modified by filters.

`CALCULATETABLE ( <Table> [, <Filter> [, <Filter> [, … ] ] ] )`
