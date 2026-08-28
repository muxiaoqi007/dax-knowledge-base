---
title: "HASONEFILTER"
function: "hasonefilter"
category: "Information"
url: "https://dax.guide/hasonefilter/"
source: "dax.guide"
重要度:
难度:
---

# HASONEFILTER DAX Function (Information)

Returns true when the specified table or column has one and only one value resulting from direct filter(s).

## Syntax

HASONEFILTER ( <ColumnName> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName |  | The column to check the filter info. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

TRUE when the number of directly filtered values on ColumnName is one; otherwise returns FALSE.

## Remarks

This function is similar to [[HASONEVALUE]] with the difference that [[HASONEVALUE]] works based on cross-filters while HASONEFILTER works by a direct filter.

HASONEFILTER corresponds to the following code:

```dax


COUNTROWS ( FILTERS ( <ColumnName> ) ) = 1

```

[» 2 related functions](#alt)  

## Examples

```dax


--  HASONEFILTER checks whether a column is being filtered

--  with only one value.

--

--  Filters on non-existing values are not considered

DEFINE

    MEASURE Sales[Audio only] =

        CALCULATE (

            HASONEFILTER ( 'Product'[Category] ),

            'Product'[Category] = "Audio"

        )

    MEASURE Sales[Audio and computers] =

        CALCULATE (

            HASONEFILTER ( 'Product'[Category] ),

            'Product'[Category] IN { "Audio", "Computers" }

        )

    MEASURE Sales[Audio and bananas] =

        CALCULATE (

            HASONEFILTER ( 'Product'[Category] ),

            'Product'[Category] IN { "Audio", "Bananas" }

        )

EVALUATE

{

     ( "Audio only", [Audio only] ),

     ( "Audio and computers", [Audio and computers] ),

     ( "Audio and bananas", [Audio and bananas] )

}

```

| Value1 | Value2 |
| --- | --- |
| Audio only | true |
| Audio and computers | false |
| Audio and bananas | true |

```dax


--  HASONEFILTER is not HASONEVALUE.

--  A column can have only one value visible because of

--  cross-filtering.

DEFINE

    MEASURE Sales[Has One Filter] =

        CALCULATE (

            HASONEFILTER ( 'Product'[Category] ),

            'Product'[Product Name] = "Contoso 512MB MP3 Player E51 Silver"

        )

    MEASURE Sales[Has One Value] =

        CALCULATE (

            HASONEVALUE ( 'Product'[Category] ),

            'Product'[Product Name] = "Contoso 512MB MP3 Player E51 Silver"

        )

EVALUATE

{

     ( "Has One Filter", [Has One Filter] ),

     ( "Has One Value", [Has One Value] )

}

```

| Value1 | Value2 |
| --- | --- |
| Has One Filter | false |
| Has One Value | true |

```dax


--  A column can be filtered without showing any values,

--  because of cross-filters

DEFINE

    MEASURE Sales[Has One Filter] =

        CALCULATE (

            HASONEFILTER ( 'Product'[Category] ),

            'Product'[Category] = "Home Appliances",

            'Product'[Product Name] = "Contoso 512MB MP3 Player E51 Silver"

        )

    MEASURE Sales[Are there any products?] =

        CALCULATE (

            COUNTROWS ( 'Product' ) > 0,

            'Product'[Category] = "Home Appliances",

            'Product'[Product Name] = "Contoso 512MB MP3 Player E51 Silver"

        )

EVALUATE

{

     ( "Has One Filter", [Has One Filter] ),

     ( "Are there any products?", [Are there any products?] )

}

```

| Value1 | Value2 |
| --- | --- |
| Has One Filter | true |
| Are there any products? | false |

## Related functions

Other related functions are:

- [[HASONEVALUE]]
- [[FILTERS]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/hasonefilter-function-dax](https://docs.microsoft.com/en-us/dax/hasonefilter-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
