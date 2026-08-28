---
title: "CROSSFILTER"
function: "crossfilter"
category: "Relationship management"
url: "https://dax.guide/crossfilter/"
source: "dax.guide"
重要度:
难度:
---

# CROSSFILTER DAX Function (Relationship management)

Specifies cross filtering direction to be used in the evaluation of a DAX expression. The relationship is defined by naming, as arguments, the two columns that serve as endpoints.

## Syntax

CROSSFILTER ( <LeftColumnName>, <RightColumnName>, <CrossFilterType> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| LeftColumnName |  | Left Column. |
| RightColumnName |  | Right Column. |
| CrossFilterType |  | The third argument to the CROSSFILTER function should be 0 for None, or 1 for OneWay, or 2 for Both, or 3 for OneWay\_RightFiltersLeft, or 4 for OneWay\_LeftFiltersRight. It is also possible to use the words None, OneWay, Both, OneWay\_RightFiltersLeft, OneWay\_LeftFiltersRight. |

## Return values

The function returns no value. The function only sets the cross-filtering direction for the indicated relationship, for the duration of the query.

## Remarks

In the case of a 1:1 relationship, there is no difference between the one and both direction.

CROSSFILTER can only be used in functions that take a filter predicate as an argument, for example: [[CALCULATE]], [[CALCULATETABLE]], [[CLOSINGBALANCEMONTH]], [[CLOSINGBALANCEQUARTER]], [[CLOSINGBALANCEYEAR]], [[OPENINGBALANCEMONTH]], [[OPENINGBALANCEQUARTER]], [[OPENINGBALANCEYEAR]], [[TOTALMTD]], [[TOTALQTD]] and [[TOTALYTD]] functions.

CROSSFILTER uses existing relationships in the model, identifying relationships by their ending point columns.

In CROSSFILTER, the cross-filtering setting of a relationship is not important; that is, whether the relationship is set to filter one, or both directions in the model does not affect the usage of the function. CROSSFILTER will override any existing cross-filtering setting.

An error is returned if any of the columns named as an argument is not part of a relationship or the arguments belong to different relationships.  
If [[CALCULATE]] expressions are nested, and more than one [[CALCULATE]] expression contains a CROSSFILTER function, then the innermost CROSSFILTER is the one that prevails in case of a conflict or ambiguity.

The arguments OneWay\_RightFiltersLeft and OneWay\_LeftFiltersRight can be used in many-to-many and one-to-many relationship types, but not in the one-to-one relationship type.  
When the cross-filter type OneWay\_RightFiltersLeft or OneWay\_LeftFiltersRight is used in a one-to-many relationship type, it must be consistent with the only allowed filter propagation, which is one-to-many. If the requested direction is the opposite one, CROSSFILTER returns an error.

CROSSFILTER only changes the cross filter direction applied when the relationship is active. CROSSFILTER does not change the active state of the relationship: [[USERELATIONSHIP]] must be used to activate an inactive relationship,.

CROSSFILTER cannot be used when it changes the default filter propagation from a table that has Row-level security (RLS) applied. Consider using [[TREATAS]] as a workaround in those cases.

[» 5 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  CROSSFILTER changes the cross-filter direction of a relationship

--  The arguments are the columns involved in the relationship and

--  the cross-filter direction, which can be BOTH, ONEWAY, NONE

DEFINE

    MEASURE Sales[Customers] =

        COUNTROWS ( Customer )

    MEASURE Sales[CustomersFiltered] =

        CALCULATE (

            [Customers],

            CROSSFILTER ( Sales[CustomerKey], Customer[CustomerKey], BOTH )

        )

    MEASURE Sales[ProductsDoesNotFilter] =

        CALCULATE (

            [Customers],

            CROSSFILTER ( Sales[CustomerKey], Customer[CustomerKey], BOTH ),

            CROSSFILTER ( Sales[ProductKey], 'Product'[ProductKey], NONE )

        )

EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Brand],

    "Customers", [Customers],

    "Customers Buying", [CustomersFiltered],

    "Products does not filter Sales", [ProductsDoesNotFilter]

)

```

| Brand | Customers | Customers Buying | Products does not filter Sales |
| --- | --- | --- | --- |
| Contoso | 18,869 | 4,346 | 18,869 |
| Wide World Importers | 18,869 | 517 | 18,869 |
| Northwind Traders | 18,869 | 1,002 | 18,869 |
| Adventure Works | 18,869 | 2,587 | 18,869 |
| Southridge Video | 18,869 | 5,200 | 18,869 |
| Litware | 18,869 | 994 | 18,869 |
| Fabrikam | 18,869 | 526 | 18,869 |
| Proseware | 18,869 | 495 | 18,869 |
| A. Datum | 18,869 | 1,144 | 18,869 |
| The Phone Company | 18,869 | 318 | 18,869 |
| Tailspin Toys | 18,869 | 4,278 | 18,869 |

## Related articles

Learn more about CROSSFILTER in the following articles:

- [**Many-to-many relationships in Power BI and Excel 2016**](https://www.sqlbi.com/articles/many-to-many-relationships-in-power-bi-and-excel-2016/)

  The new DAX available in Excel 2016 and the data model in Power BI and Analysis Services 2016 offer tools to manage many-to-many relationships in a more efficient way than previous version, as described in this article. [» Read more](https://www.sqlbi.com/articles/many-to-many-relationships-in-power-bi-and-excel-2016/)
- [**Bidirectional relationships and ambiguity in DAX**](https://www.sqlbi.com/articles/bidirectional-relationships-and-ambiguity-in-dax/)

  Activating bidirectional cross-filter in a Tabular data model might create ambiguous paths in the chain of relationships, resulting in very dangerous models as numbers become unpredictable. This article provides a deep explanation of the kind of ambiguity that might appear… [» Read more](https://www.sqlbi.com/articles/bidirectional-relationships-and-ambiguity-in-dax/)
- [**Different options to model many-to-many relationships in Power BI and Tabular**](https://www.sqlbi.com/articles/different-options-to-model-many-to-many-relationships-in-power-bi-and-tabular/)

  There are two options to model many-to-many relationships using Tabular and Power BI: you can use either a regular bidirectional filter relationship, or a limited unidirectional relationship. In this article, we compare the performance of both options. [» Read more](https://www.sqlbi.com/articles/different-options-to-model-many-to-many-relationships-in-power-bi-and-tabular/)
- [**Measuring the impact of promotions on sales in Power BI**](https://www.sqlbi.com/articles/measuring-the-impact-of-promotions-on-sales-in-power-bi/)

  This article describes the data model and DAX measures to analyze the effectiveness of campaigns, by separating attributed sales (directly linked to a campaign) from influenced sales (all sales of products participating in campaigns, regardless of attribution). [» Read more](https://www.sqlbi.com/articles/measuring-the-impact-of-promotions-on-sales-in-power-bi/)
- [**Show transaction details on the matrix visual in Power BI**](https://www.sqlbi.com/articles/show-transaction-details-on-the-matrix-visual-in-power-bi/)

  This article shows how to create a DAX measure that displays information from multiple columns in a business entity or transaction, into a single column of a matrix. [» Read more](https://www.sqlbi.com/articles/show-transaction-details-on-the-matrix-visual-in-power-bi/)

## Related functions

Other related functions are:

- [[USERELATIONSHIP]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/crossfilter-function](https://docs.microsoft.com/en-us/dax/crossfilter-function?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
