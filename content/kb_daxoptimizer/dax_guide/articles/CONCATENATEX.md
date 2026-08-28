---
title: "CONCATENATEX"
function: "concatenatex"
category: "Text"
url: "https://dax.guide/concatenatex/"
source: "dax.guide"
重要度:
难度:
---

# CONCATENATEX DAX Function (Text)

Evaluates expression for each row on the table, then return the concatenation of those values in a single string result, seperated by the specified delimiter.

## Syntax

CONCATENATEX ( <Table>, <Expression> [, <Delimiter>] [, <OrderBy\_Expression> [, [<Order>] [, <OrderBy\_Expression> [, [<Order>] [, … ] ] ] ] ] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | The table containing the rows for which the expression will be evaluated. || Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | The expression to be evaluated for each row of the table. |
| Delimiter | Optional | The delimiter to be concatenated with expression. |
| OrderBy\_Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression | Optional Repeatable | Expression to be used for sorting the table. |
| Order | Optional Repeatable | The order to be applied. 0/FALSE/DESC – descending; 1/TRUE/ASC – ascending. |

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

A text string with the concatenated values.

## Remarks

This function iterates the rows in the table provided by the first argument and for each row it executes the expression provided in the second argument. All the expressions are concatenated using the separator provided as third argument.

The default order is ASC (ascending) when OrderBy\_Expression argument is present and the corresponding Order argument is not specified.

If the order OrderBy\_Expression argument is not specified, the items in Table can be evaluated in any order, returning a string that does not reflect any existing physical order in the source data.

[» 7 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  CONCATENATEX is an iterator that produces the concatenation

--  of expressions evaluated during the iteration.

--  You provide the expression, a separator and optional

--  sorting expressions.

EVALUATE

ADDCOLUMNS (

    VALUES ( 'Product'[Category] ),

    "Category colors", 

        CONCATENATEX ( 

            CALCULATETABLE ( VALUES ( 'Product'[Color] ) ),

            Product[Color],

            ", ",             -- Separator (optional)

            Product[Color],   -- Sorting expression (optional)

            ASC               -- Sorting direction (optional)

        )

)

```

| Category | Category colors |
| --- | --- |
| Audio | Black, Blue, Green, Orange, Pink, Purple, Red, Silver, White, Yellow |
| TV and Video | Black, Brown, Silver, White |
| Computers | Black, Blue, Brown, Gold, Green, Grey, Orange, Pink, Red, Silver, White, Yellow |
| Cameras and camcorders | Azure, Black, Blue, Gold, Green, Grey, Orange, Pink, Purple, Red, Silver, Silver Grey, White, Yellow |
| Cell phones | Black, Gold, Grey, Pink, Red, Silver, Transparent, White |
| Music, Movies and Audio Books | Black, Blue, Gold, Grey, Red, Silver, White, Yellow |
| Games and Toys | Black, Blue, Gold, Green, Grey, Pink, Purple, Red, Silver, White, Yellow |
| Home Appliances | Black, Blue, Brown, Gold, Green, Grey, Orange, Pink, Purple, Red, Silver, White, Yellow |

## Related articles

Learn more about CONCATENATEX in the following articles:

- [**Displaying Nth Element in DAX**](https://www.sqlbi.com/articles/displaying-nth-element-in-dax/)

  This article describes how to create a measure displaying the name or value of an element that has a specific ranking, with different option for managing ties. [» Read more](https://www.sqlbi.com/articles/displaying-nth-element-in-dax/)
- [**Displaying filter context in Power BI Tooltips**](https://www.sqlbi.com/articles/displaying-filter-context-in-power-bi-tooltips/)

  This article describes how to display the filter context applied to a calculation using a special DAX measure in Power BI Tooltips. [» Read more](https://www.sqlbi.com/articles/displaying-filter-context-in-power-bi-tooltips/)
- [**Table and column references using DAX variables**](https://www.sqlbi.com/articles/table-and-column-references-using-dax-variables/)

  This article describes how to correctly use column references when manipulating tables assigned to DAX variables, avoiding syntax errors and making the code easier to read and maintain. [» Read more](https://www.sqlbi.com/articles/table-and-column-references-using-dax-variables/)
- [**Using CONCATENATEX in measures**](https://www.sqlbi.com/articles/using-concatenatex-in-measures/)

  This article showcases the use of CONCATENATEX, a handy DAX function to return a list of values in a measure. [» Read more](https://www.sqlbi.com/articles/using-concatenatex-in-measures/)
- [**Reading active Power BI security roles in DAX**](https://www.sqlbi.com/articles/reading-active-power-bi-security-roles-in-dax/)

  This article describes how to read the active security roles in a Tabular model for Power BI or Analysis Services. This way, you can use measures and calculation groups to customize a report based dynamically on security roles active for the current user. [» Read more](https://www.sqlbi.com/articles/reading-active-power-bi-security-roles-in-dax/)
- [**Show transaction details on the matrix visual in Power BI**](https://www.sqlbi.com/articles/show-transaction-details-on-the-matrix-visual-in-power-bi/)

  This article shows how to create a DAX measure that displays information from multiple columns in a business entity or transaction, into a single column of a matrix. [» Read more](https://www.sqlbi.com/articles/show-transaction-details-on-the-matrix-visual-in-power-bi/)
- [**Debugging DAX variables using TOJSON and TOCSV**](https://www.sqlbi.com/articles/debugging-dax-variables-using-tojson-and-tocsv/)

  This article describes how to use the TOJSON and TOCSV functions to inspect the content of intermediate table variables when debugging a DAX measure. [» Read more](https://www.sqlbi.com/articles/debugging-dax-variables-using-tojson-and-tocsv/)

## Related functions

Other related functions are:

- [[CONCATENATE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Francis Romio, Airat Gabdrakhmanov

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/concatenatex-function-dax](https://docs.microsoft.com/en-us/dax/concatenatex-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
