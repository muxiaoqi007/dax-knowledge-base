---
title: "DIVIDE"
function: "divide"
category: "Math and Trig"
url: "https://dax.guide/divide/"
source: "dax.guide"
重要度:
难度:
---

# DIVIDE DAX Function (Math and Trig)

Safe Divide function with ability to handle divide by zero case.

## Syntax

DIVIDE ( <Numerator>, <Denominator> [, <AlternateResult>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Numerator |  | Numerator. |
| Denominator |  | Denominator. |
| AlternateResult | Optional | Optional. The alternate result to return when dividing by zero. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

Result of the division between Numerator and Denominator, or AlternateResult in case there is a division by zero.

## Remarks

Alternate result on divide by 0 must be a constant. By default, the AlternateResult argument is [[BLANK]].  
DIVIDE is faster than an [[IF]] statement checking whether the denominator is zero. However, DIVIDE is executed in the formula engine and it is not as fast as a [native division](https://dax.guide/op/division/).

[» 3 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  DIVIDE performs safe division protecting from division by

--  zero. In case of zero denominator, it returns its third 

--  argument that defaults to a blank.

DEFINE

    MEASURE Sales[Unprotected Growth %] = 

        VAR CY = [Sales Amount]

        VAR PY = CALCULATE ( [Sales Amount], SAMEPERIODLASTYEAR( 'Date'[Date] ) )

        RETURN (CY - PY) / PY

    MEASURE Sales[Protected Growth %] = 

        VAR CY = [Sales Amount]

        VAR PY = CALCULATE ( [Sales Amount], SAMEPERIODLASTYEAR( 'Date'[Date] ) )

        RETURN DIVIDE ( CY - PY, PY, BLANK () )

EVALUATE

SUMMARIZECOLUMNS ( 

    'Date'[Calendar Year],

    "Unprotected Growth %", [Unprotected Growth %],

    "Protected Growth %",   [Protected Growth %]

)



```

| Calendar Year | Unprotected Growth % | Protected Growth % |
| --- | --- | --- |
| 2007-01-01 | Infinity | (Blank) |
| 2008-01-01 | -0.12222543928588246 | -12.22% |
| 2009-01-01 | -0.057795348753533114 | -5.78% |
| 2010-01-01 | -1 | -100.00% |

## Related articles

Learn more about DIVIDE in the following articles:

- [**DIVIDE Performance**](https://www.sqlbi.com/articles/divide-performance/)

  The DIVIDE function in DAX is usually faster to avoid division-by-zero errors than the simple division operator. However, there are exceptions to this rule, described in this article through a simple performance analysis. [» Read more](https://www.sqlbi.com/articles/divide-performance/)
- [**From SQL to DAX: Implementing NULLIF and COALESCE in DAX**](https://www.sqlbi.com/articles/from-sql-to-dax-implementing-nullif-and-coalesce-in-dax/)

  This article describes how to implement a syntax equivalent to the T-SQL function NULLIF and the ANSI SQL function COALESCE, in DAX. [» Read more](https://www.sqlbi.com/articles/from-sql-to-dax-implementing-nullif-and-coalesce-in-dax/)
- [**Measuring the impact of promotions on sales in Power BI**](https://www.sqlbi.com/articles/measuring-the-impact-of-promotions-on-sales-in-power-bi/)

  This article describes the data model and DAX measures to analyze the effectiveness of campaigns, by separating attributed sales (directly linked to a campaign) from influenced sales (all sales of products participating in campaigns, regardless of attribution). [» Read more](https://www.sqlbi.com/articles/measuring-the-impact-of-promotions-on-sales-in-power-bi/)

## Related functions

Other related functions are:

- [[QUOTIENT]]
- [[IFERROR]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/divide-function-dax](https://docs.microsoft.com/en-us/dax/divide-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
