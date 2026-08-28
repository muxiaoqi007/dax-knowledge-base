---
title: "PRODUCTX"
function: "productx"
category: "Aggregation"
url: "https://dax.guide/productx/"
source: "dax.guide"
重要度:
难度:
---

# PRODUCTX DAX Function (Aggregation)

Returns the product of an expression values in a table.

## Syntax

PRODUCTX ( <Table>, <Expression> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Table  [Iterator](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/) |

  | Table over which the Expression will be evaluated. || Expression  [Row Context](https://www.sqlbi.com/articles/row-context-and-filter-context-in-dax/)  By Expression |  | Expression to evaluate for each row of the table. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

The product of the Expression evaluated for each row in the Table.

## Remarks

PRODUCTX ignores blank expressions, the multiplication operator does not.

By using  ^ -1 skipping the first row, you can use PRODUCTX to obtain the result of a DIVIDEX function (which is not available in DAX).

```dax


-- Apply PRODUCTX to Table[Column]

PRODUCTX ( Table, Table[Column] )



-- Apply an hypothetical DIVIDEX function to Table[Column] by using PRODUCTX

PRODUCTX ( 

    Table, 

    -- Avoid the IF in the iterator for performance reasons

    Table[Column] ^ (1 - 2 * ( Table[RowNumber] > 1 ) ) 

)

```

[» 2 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax
 

-- PRODUCT is the short version of PRODUCTX

-- PRODUCTX multiplies values in the rows it scans

--

-- The report shows the actualized sales in 2009 by applying 

-- the inflation rate in sales made in previous years

DEFINE

    MEASURE Rates[Compound Inflation Rate] =

        PRODUCTX ( Rates, 1 + Rates[InflationRate] )

    MEASURE Sales[Sales Amount] =

        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

    MEASURE Sales[Actualized Sales 2009] =

        SUMX (

            VALUES ( 'Date'[Calendar Year Number] ),

            VAR ReferenceYear = 2009

            VAR BaseYear = 'Date'[Calendar Year Number]

            VAR CompoundRate =

                CALCULATE (

                    COALESCE ( [Compound Inflation Rate], 1 ),

                    Rates[Year] >= BaseYear && Rates[Year] < ReferenceYear

                )

            RETURN [Sales Amount] * CompoundRate

        )

    MEASURE Sales[Adjustment %] =

        VAR ReferenceYear = 2009

        VAR BaseYear = SELECTEDVALUE ( 'Date'[Calendar Year Number] )

        VAR CompoundRate =

            CALCULATE (

                COALESCE ( [Compound Inflation Rate], 1 ),

                Rates[Year] >= BaseYear && Rates[Year] < ReferenceYear

            )

        RETURN IF ( 

            NOT ISBLANK ( BaseYear ) && NOT ISBLANK ( [Sales Amount] ), 

            CompoundRate 

        )

        

EVALUATE Rates 



EVALUATE

SUMMARIZECOLUMNS (

    ROLLUPADDISSUBTOTAL ( 'Date'[Calendar Year], "IsTotal" ),

    "Sales Amount", [Sales Amount],

    "Adjustment %", [Adjustment %],

    "Actualized Sales 2009", [Actualized Sales 2009]

)

ORDER BY [IsTotal], [Calendar Year]

```

| Year | InflationRate |
| --- | --- |
| 2,005 | 0.03 |
| 2,006 | 0.03 |
| 2,007 | 0.03 |
| 2,008 | 0.04 |
| 2,009 | 0.00 |
| 2,010 | 0.02 |

| Calendar Year | IsTotal | Sales Amount | Adjustment % | Actualized Sales 2009 |
| --- | --- | --- | --- | --- |
| 2007-01-01 | false | 11,309,946.12 | 106.80% | 12,078,959.12 |
| 2008-01-01 | false | 9,927,582.99 | 103.84% | 10,308,802.18 |
| 2009-01-01 | false | 9,353,814.87 | 100.00% | 9,353,814.87 |
| (Blank) | true | 30,591,343.98 | (Blank) | 31,741,576.16 |

## Related articles

Learn more about PRODUCTX in the following articles:

- [**Computing the future value of an investment based on compound growth in DAX**](https://www.sqlbi.com/articles/computing-the-future-value-of-an-investment-based-on-compound-growth-in-dax/)

  This article describes how to write efficient DAX expressions that compute the compound interest of incremental investments made throughout the holding period. [» Read more](https://www.sqlbi.com/articles/computing-the-future-value-of-an-investment-based-on-compound-growth-in-dax/)
- [**Using EXPAND and COLLAPSE in visual calculations**](https://www.sqlbi.com/articles/using-expand-and-collapse-in-visual-calculations/)

  This article provides examples of visual calculations where the use of EXPAND and COLLAPSE is required to obtain the correct result. [» Read more](https://www.sqlbi.com/articles/using-expand-and-collapse-in-visual-calculations/)

## Related functions

Other related functions are:

- [[PRODUCT]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Ashley Garoutte, Steve Yamashiro, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/productx-function-dax](https://docs.microsoft.com/en-us/dax/productx-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
