---
title: "SELECTEDVALUE"
function: "selectedvalue"
category: "Filter"
url: "https://dax.guide/selectedvalue/"
source: "dax.guide"
重要度:
难度:
---

# SELECTEDVALUE DAX Function (Filter)

Returns the value when there’s only one value in the specified column, otherwise returns the alternate result.

## Syntax

SELECTEDVALUE ( <ColumnName> [, <AlternateResult>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName |  | The column from which a single value is to be returned. |
| AlternateResult | Optional | The value that is returned when there is no value or more than one value in the specified column; if omitted, [[BLANK]] is returned. |

## Return values

Scalar A single value of any type.

The value when the context for ColumnName has been filtered down to one distinct value only. Else, AlternateResult.

## Remarks

A similar expression for

```dax


SELECTEDVALUE( <columnName>, <alternateResult> )

```

is

```dax


IF ( HASONEVALUE( <columnName> ), VALUES( <columnName> ), <alternateResult> )

```

SELECTEDVALUE cannot be directly used to get the selected item on a column used by the [Fields Parameter feature in Power BI](https://www.sqlbi.com/articles/fields-parameters-in-power-bi/).  
Instead, the required code is the following:

```dax


VAR __SelectedValue = 

    SELECTCOLUMNS (

        SUMMARIZE ( Parameter, Parameter[Parameter], Parameter[Parameter Fields] ),

        Parameter[Parameter]

    )

RETURN IF ( NOT ISEMPTY ( __SelectedValue ), __SelectedValue )

```

[» 7 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

SELECTEDVALUE returns the value currently visible in the filter context for a column if there is only one value filtered. Otherwise, it returns the default argument.

```dax


-- Shows "unknown" when there are more values in the filter context

-- for the columns specified in the first argument of SELECTEDVALUE

EVALUATE

CALCULATETABLE (

    SUMMARIZECOLUMNS (

        Product[Brand],

        ROLLUPADDISSUBTOTAL ( 'Product'[Category], "Total Category" ),

        

        "Current Brand",    SELECTEDVALUE ( 'Product'[Brand],        "**Unknown Brand**" ),

        "Current Category", SELECTEDVALUE ( 'Product'[Category],     "**Unknown Category**" ),

        "Current Product",  SELECTEDVALUE ( 'Product'[Product Name], "**Unknown Product**" )

    ),

    

    TREATAS ( { "Litware", "A. Datum" }, Product[Brand] )

)



```

| Product[Brand] | Product[Category] | Total Category | Current Brand | Current Category | Current Product |
| --- | --- | --- | --- | --- | --- |
| Litware | TV and Video | false | Litware | TV and Video | \*\*Unknown Product\*\* |
| A. Datum | Cameras and camcorders | false | A. Datum | Cameras and camcorders | \*\*Unknown Product\*\* |
| Litware | Home Appliances | false | Litware | Home Appliances | \*\*Unknown Product\*\* |
| Litware | (Blank) | true | Litware | \*\*Unknown Category\*\* | \*\*Unknown Product\*\* |
| A. Datum | (Blank) | true | A. Datum | Cameras and camcorders | \*\*Unknown Product\*\* |

```dax


--  SELECTEDVALUE is equivalent to a more complex combination 

--  with HASONEVALUE and VALUES.

EVALUATE

SUMMARIZECOLUMNS (

    'Product'[Brand],

    "Current Brand", 

        SELECTEDVALUE (

            'Product'[Brand], 

            "**Unknown**" 

        ),

    "Current Brand 2",

        IF (

            HASONEVALUE ( 'Product'[Brand] ),

            VALUES ( 'Product'[Brand] ),

            "**Unknown**"

        )

)



```

| Product[Brand] | Current Brand | Current Brand 2 |
| --- | --- | --- |
| Contoso | Contoso | Contoso |
| Wide World Importers | Wide World Importers | Wide World Importers |
| Northwind Traders | Northwind Traders | Northwind Traders |
| Adventure Works | Adventure Works | Adventure Works |
| Southridge Video | Southridge Video | Southridge Video |
| Litware | Litware | Litware |
| Fabrikam | Fabrikam | Fabrikam |
| Proseware | Proseware | Proseware |
| A. Datum | A. Datum | A. Datum |
| The Phone Company | The Phone Company | The Phone Company |
| Tailspin Toys | Tailspin Toys | Tailspin Toys |

## Related articles

Learn more about SELECTEDVALUE in the following articles:

- [**Using the SELECTEDVALUE function in DAX**](https://www.sqlbi.com/articles/using-the-selectedvalue-function-in-dax/)

  This article describes how the SELECTEDVALUE DAX function simplifies the syntax required in many scenarios where you need to read a single value selected in the filter context. [» Read more](https://www.sqlbi.com/articles/using-the-selectedvalue-function-in-dax/)
- [**Showing the top 5 products and Other row**](https://www.sqlbi.com/articles/showing-the-top-5-products-and-others-row/)

  This article shows how to add an additional “other” row to the selection obtained using the Top N filter in a Power BI report. [» Read more](https://www.sqlbi.com/articles/showing-the-top-5-products-and-others-row/)
- [**Comparing with previous selected time period in DAX**](https://www.sqlbi.com/articles/comparing-with-previous-selected-time-period-in-dax/)

  This article describes how you can create a comparison with the previous time period in a visualization, regardless of whether the time periods are consecutive or not. [» Read more](https://www.sqlbi.com/articles/comparing-with-previous-selected-time-period-in-dax/)
- [**Using SELECTEDVALUE with Fields Parameters in Power BI**](https://www.sqlbi.com/blog/marco/2022/06/11/using-selectedvalue-with-fields-parameters-in-power-bi/)

  If you try to use SELECTEDVALUE on the visible column of the table generated by the Fields Parameters feature in Power BI, you get the following error: Calculation error in measure ‘Sales'[Selection]: Column [Parameter] is part of composite key, but… [» Read more](https://www.sqlbi.com/blog/marco/2022/06/11/using-selectedvalue-with-fields-parameters-in-power-bi/)
- [**Finding products without sales by using DAX**](https://www.sqlbi.com/articles/finding-products-without-sales-by-using-dax/)

  This article analyzes the performance of different DAX techniques to identify any products without sales in an area or a time period. [» Read more](https://www.sqlbi.com/articles/finding-products-without-sales-by-using-dax/)
- [**Filtering calculation items in a slicer**](https://www.sqlbi.com/articles/filtering-calculation-items-in-a-slicer/)

  When a slicer contains many items, developers can filter the most relevant items using another slicer. The scenario is easily solved with a many-to-many relationship if the source is a regular table. Still, it requires some DAX coding if the slicer contains calculation items. [» Read more](https://www.sqlbi.com/articles/filtering-calculation-items-in-a-slicer/)
- [**Show transaction details on the matrix visual in Power BI**](https://www.sqlbi.com/articles/show-transaction-details-on-the-matrix-visual-in-power-bi/)

  This article shows how to create a DAX measure that displays information from multiple columns in a business entity or transaction, into a single column of a matrix. [» Read more](https://www.sqlbi.com/articles/show-transaction-details-on-the-matrix-visual-in-power-bi/)

## Related functions

Other related functions are:

- [[HASONEVALUE]]
- [[IF]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/selectedvalue-function](https://docs.microsoft.com/en-us/dax/selectedvalue-function?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
