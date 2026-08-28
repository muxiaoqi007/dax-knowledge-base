---
title: "CURRENCY"
function: "currency"
category: "Math and Trig"
url: "https://dax.guide/currency/"
source: "dax.guide"
重要度:
难度:
---

# CURRENCY DAX Function (Math and Trig)

Returns the value as a currency data type.

## Syntax

CURRENCY ( <Value> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Value |  | A scalar expression to be converted to currency data type. |

## Return values

Scalar A single [currency](https://dax.guide/dt/currency/) value.

The value of the expression evaluated and returned as a currency type value.

## Remarks

The CURRENCY function rounds up the 5th significant decimal, in value, to return the 4th decimal digit; rounding up occurs if the 5th significant decimal is equal to or larger than 5. For example, if the value is 3.6666666666666 then converting to currency returns $3.6667; however, if the value is 3.0123456789 then converting to currency returns $3.0123.

If the data type of the expression is [Boolean](https://dax.guide/dt/boolean/) then CURRENCY() will return $1.0000 for True values and $0.0000 for False values.

If the data type of the expression is [String](https://dax.guide/dt/string/) then CURRENCY() will try to convert string to a number; if the conversion succeeds the number will be converted to currency, otherwise, an error is returned.

If the data type of the expression is [DateTime](https://dax.guide/dt/datetime/) then CURRENCY() will convert the [DateTime](https://dax.guide/dt/datetime/) value to a number and that number to currency. [DateTime](https://dax.guide/dt/datetime/) values have an integer part that represents the number of days between the given date and 1900-03-01 and a fraction that represents the fraction of a day (where 12 hours or noon is 0.5 day). If the value of the expression is not a proper [DateTime](https://dax.guide/dt/datetime/) value, an error is returned.

The CURRENCY() function has the same behavior as calling [[CONVERT]] with a CURRENCY argument. The two following statements are equivalent:

```dax


CURRENCY ( <expression> )

CONVERT ( <expression>, CURRENCY )

```

[» 4 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  CURRENCY converts any expression in a CURRENCY datatype

--  The Currency data type is called Fixed Decimal Number in Power BI

--  This data type stores 4 digits after the decimal point

EVALUATE 

    {

        ( "Currency 1", FORMAT ( CURRENCY ( 10 ), "#.00000" ) ),

        ( "Currency 2", FORMAT ( CURRENCY ( TRUE ), "#.00000" ) ),

        ( "Currency 3", FORMAT ( CURRENCY ( "1950.00" ), "#.00000" ) ),

        ( "Currency 4", FORMAT ( CURRENCY ( 190.29876 ), "#.00000" ) )

    }

```

| Value1 | Value2 |
| --- | --- |
| 2001-01-01 | 10.00000 |
| 2001-02-01 | 1.00000 |
| 2001-03-01 | 1950.00000 |
| 2001-04-01 | 190.29880 |

## Related articles

Learn more about CURRENCY in the following articles:

- [**Understanding numeric data type conversions in DAX**](https://www.sqlbi.com/articles/understanding-numeric-data-type-conversions-in-dax/)

  This article describes how DAX automatically converts data types in arithmetic operations. These small details can cause and explain differences in results when using the same operations in other languages. [» Read more](https://www.sqlbi.com/articles/understanding-numeric-data-type-conversions-in-dax/)
- [**Differences between INT and CONVERT in DAX**](https://www.sqlbi.com/articles/differences-between-int-and-convert-in-dax/)

  This article describes the small differences between INT and CONVERT in DAX that may end up returning different results in arithmetic expressions. [» Read more](https://www.sqlbi.com/articles/differences-between-int-and-convert-in-dax/)
- [**Rounding errors with different data types in DAX**](https://www.sqlbi.com/articles/rounding-errors-with-different-data-types-in-dax/)

  This article describes the possible rounding differences that can appear in DAX. They are related to the data types and the operation being performed: knowing these details helps you write more robust DAX formulas and avoid errors in comparisons. [» Read more](https://www.sqlbi.com/articles/rounding-errors-with-different-data-types-in-dax/)
- [**Impact of data types in DAX arithmetical calculations**](https://www.sqlbi.com/articles/impact-of-data-types-in-dax-arithmetical-calculations/)

  The choice of data types can affect precision and performance because of internal conversions. This article shows how the right choice of data type and DAX code makes a significant difference. [» Read more](https://www.sqlbi.com/articles/impact-of-data-types-in-dax-arithmetical-calculations/)

## Related functions

Other related functions are:

- [[INT]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/currency-function-dax](https://docs.microsoft.com/en-us/dax/currency-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
