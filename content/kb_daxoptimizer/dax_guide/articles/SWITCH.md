---
title: "SWITCH"
function: "switch"
category: "Logical"
url: "https://dax.guide/switch/"
source: "dax.guide"
重要度:
难度:
---

# SWITCH DAX Function (Logical)

Returns different results depending on the value of an expression.

## Syntax

SWITCH ( <Expression>, <Value>, <Result> [, <Value>, <Result> [, … ] ] [, <Else>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Expression |  | The expression to be evaluated. |
| Value | Repeatable | If expression has this value the corresponding result will be returned. |
| Result | Repeatable | The result to be returned if Expression has corresponding value. |
| Else | Optional | If there are no matching values the Else value is returned. |

## Return values

Scalar A single value of any type.

A scalar value coming from one of the Result expressions, if there was a match with Value, or from the Else expression, if there was no match with any Value.

## Remarks

All result expressions and the else expression must be of the same data type.

A common use of SWITCH is to match the result of an expression with constant value:

```dax


SWITCH (

    [A],

    0, "Zero",

    1, "One",

    2, "Two",

    "Other numbers"

)

```

However, the  argument can be an expression and the initial  can be a constant.  
By using TRUE as a first argument, SWITCH can replace a list of cascading [[IF]] statements.  
The following code:

```dax


IF (

    [A] > [B], 

    "First case",

    IF ( 

        [A] = [B],

        "Second case",

        IF (

            [A] = 0,

            "Third case",

            "Fourth case"

        )

    )

)

```

can be written as:

```dax


SWITCH (

    TRUE,

    [A] > [B], "First case",

    [A] = [B], "Second case",

    [A] = 0, "Third case",

    "Fourth case"

)

```

[» 6 related articles](#articles)  
[» 3 related functions](#alt)  

## Examples

```dax


--  SWITCH evaluates its first argument (value) and then uses the next

--  remaining parameters in pair: the first element is used to match the value

--  the second as the result if there is a match.

--  The last argument, alone, provides the no-match value and it defaults 

--  to blank.

DEFINE MEASURE Sales[Discounted Sales] = 

    SUMX ( 

        VALUES ( 'Product'[Category] ),

        VAR DiscountPct = 

            SWITCH ( 

                'Product'[Category],

                "Audio", 0.15,

                "Computers", 0.2,

                "Cell phones", 0.13,

                0

            ) 

        RETURN

            [Sales Amount] * (1 - DiscountPct )

    )

EVALUATE

SUMMARIZECOLUMNS ( 

    'Product'[Category],

    "Sales Amount", [Sales Amount],

    "Discounted sales", [Discounted Sales]

)

ORDER BY [Category]

```

| Product[Category] | Sales Amount | Discounted sales |
| --- | --- | --- |
| Audio | 384,518.16 | 326,840.44 |
| Cameras and camcorders | 7,192,581.95 | 7,192,581.95 |
| Cell phones | 1,604,610.26 | 1,396,010.93 |
| Computers | 6,741,548.73 | 5,393,238.98 |
| Games and Toys | 360,652.81 | 360,652.81 |
| Home Appliances | 9,600,457.04 | 9,600,457.04 |
| Music, Movies and Audio Books | 314,206.74 | 314,206.74 |
| TV and Video | 4,392,768.29 | 4,392,768.29 |

```dax


--  A common usage of SWITCH is to use a constant as the value argument

--  and expressions in the pairs. This technique allows more flexibility

--  even though it somewhat lacks in readability.

DEFINE MEASURE Sales[Discounted Sales] = 

    SUMX ( 

        SUMMARIZE ( Sales, Sales[Net Price], Product[Category] ),

        VAR DiscountPct = 

            SWITCH ( 

                TRUE,

                Sales[Net Price] <= 150, 0.15,

                Sales[Net Price] <= 1000, 0.2,

                Product[Category] = "Audio", 0.13,

                0

            ) 

        RETURN

            [Sales Amount] * (1 - DiscountPct )

    )

EVALUATE

SUMMARIZECOLUMNS ( 

    'Product'[Category],

    "Sales Amount", [Sales Amount],

    "Discounted sales", [Discounted Sales]

)

ORDER BY [Category]

```

| Product[Category] | Sales Amount | Discounted sales |
| --- | --- | --- |
| Audio | 384,518.16 | 319,255.67 |
| Cameras and camcorders | 7,192,581.95 | 5,975,444.25 |
| Cell phones | 1,604,610.26 | 1,302,864.23 |
| Computers | 6,741,548.73 | 5,806,084.78 |
| Games and Toys | 360,652.81 | 306,554.89 |
| Home Appliances | 9,600,457.04 | 8,540,512.07 |
| Music, Movies and Audio Books | 314,206.74 | 255,356.90 |
| TV and Video | 4,392,768.29 | 3,561,271.37 |

```dax


--  Using SWITCH the first condition met defines the result.

--  In the following example, the second condition (<= 150) will never be

--  met, because the first one is less restrictive.

DEFINE MEASURE Sales[Discounted Sales] = 

    SUMX ( 

        SUMMARIZE ( Sales, Sales[Net Price], Product[Category] ),

        VAR DiscountPct = 

            SWITCH ( 

                TRUE,

                Sales[Net Price] <= 1000, 0.2,

                Sales[Net Price] <= 150, 0.15,

                Product[Category] = "Audio", 0.13,

                0

            ) 

        RETURN

            [Sales Amount] * (1 - DiscountPct )

    )

EVALUATE

SUMMARIZECOLUMNS ( 

    'Product'[Category],

    "Sales Amount", [Sales Amount],

    "Discounted sales", [Discounted Sales]

)

ORDER BY [Category]

```

| Product[Category] | Sales Amount | Discounted sales |
| --- | --- | --- |
| Audio | 384,518.16 | 307,614.53 |
| Cameras and camcorders | 7,192,581.95 | 5,961,597.72 |
| Cell phones | 1,604,610.26 | 1,283,688.21 |
| Computers | 6,741,548.73 | 5,765,868.70 |
| Games and Toys | 360,652.81 | 288,522.25 |
| Home Appliances | 9,600,457.04 | 8,489,439.38 |
| Music, Movies and Audio Books | 314,206.74 | 251,365.39 |
| TV and Video | 4,392,768.29 | 3,537,902.55 |

## Related articles

Learn more about SWITCH in the following articles:

- [**Optimizing mutually exclusive calculations**](https://www.sqlbi.com/articles/optimizing-mutually-exclusive-calculations/)

  This article describes how to optimize DAX expressions with mutually exclusive calculations that might cause slow query performance. [» Read more](https://www.sqlbi.com/articles/optimizing-mutually-exclusive-calculations/)
- [**Optimizing IF and SWITCH expressions using variables**](https://www.sqlbi.com/articles/optimizing-if-and-switch-expressions-using-variables/)

  This article describes how variables should be used in DAX expressions involving IF and SWITCH statements in order to improve performance. [» Read more](https://www.sqlbi.com/articles/optimizing-if-and-switch-expressions-using-variables/)
- [**Using field parameters and calculation groups for conditional formatting**](https://www.sqlbi.com/articles/using-field-parameters-and-calculation-groups-for-conditional-formatting/)

  This article describes how to apply conditional formatting on measures picked from a slicer and implemented using two techniques: field parameters and calculation groups. [» Read more](https://www.sqlbi.com/articles/using-field-parameters-and-calculation-groups-for-conditional-formatting/)
- [**Optimizing SWITCH on slicer selection with Group By Columns**](https://www.sqlbi.com/articles/optimizing-switch-on-slicer-selection-with-group-by-columns/)

  This article describes how to use the Group By Columns property to store the slicer selection by using the same column used in a SWITCH function to optimize the query performance. [» Read more](https://www.sqlbi.com/articles/optimizing-switch-on-slicer-selection-with-group-by-columns/)
- [**Understanding the optimization of SWITCH**](https://www.sqlbi.com/articles/understanding-the-optimization-of-switch/)

  The SWITCH function in DAX has been optimized over the years, and it is helpful to know what makes the optimization work best. [» Read more](https://www.sqlbi.com/articles/understanding-the-optimization-of-switch/)
- [**Dynamic Pareto analysis in Power BI**](https://www.sqlbi.com/articles/dynamic-pareto-analysis-in-power-bi/)

  This article describes how to implement a dynamic Pareto calculation in Power BI based on a measure that can be selected from a slicer and dynamically filtered by other slicers in the report. [» Read more](https://www.sqlbi.com/articles/dynamic-pareto-analysis-in-power-bi/)

## Related functions

Other related functions are:

- [[IF]]
- [[COALESCE]]
- [[IF.EAGER]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/switch-function-dax](https://docs.microsoft.com/en-us/dax/switch-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
