---
title: "EARLIER"
function: "earlier"
url: "https://dax.guide/earlier/"
source: "dax.guide"
重要度:
难度:
---

# EARLIER DAX Function Not recommended

Returns the value in the column prior to the specified number of table scans (default is 1).

## Syntax

EARLIER ( <ColumnName> [, <Number>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| ColumnName |  | The column that contains the desired value. |
| Number | Optional | The number of table scan. |

## Return values

Scalar A single value of any type.

The current value of row, from ColumnName, at Number of outer evaluation passes.

## Remarks

EARLIER succeeds if there is a row context prior to the beginning of the table scan. Otherwise it returns an error.  
It is recommended using variable (VAR) saving the value when it is still accessible, before a new row context hides the required row context to access the desired value.

[» 2 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


--  EARLIER evaluates a column in the outer row context, in case there

--  are multiple row contexts open in the same expression

--

--  EARLIER accepts a second argument that represents the number of steps

--  EARLIEST retrieves the first ever row context

EVALUATE

ADDCOLUMNS (

    VALUES ( Customer[Yearly Income] ),

    "Customers", CALCULATE ( COUNTROWS ( Customer ) ),

    "RT Customers",

        COUNTROWS (

            FILTER (

                Customer,

                Customer[Yearly Income] <= EARLIER ( Customer[Yearly Income] )

            )

        )

)

ORDER BY [Yearly Income]



```

| Yearly Income | Customers | RT Customers |
| --- | --- | --- |
| 10,000 | 1,155 | 1,155 |
| 20,000 | 1,767 | 2,922 |
| 30,000 | 2,287 | 5,209 |
| 40,000 | 2,747 | 7,956 |
| 50,000 | 670 | 8,626 |
| 60,000 | 3,127 | 11,753 |
| 70,000 | 2,349 | 14,102 |
| 80,000 | 1,342 | 15,444 |
| 90,000 | 842 | 16,286 |
| 100,000 | 571 | 16,857 |
| 110,000 | 474 | 17,331 |
| 120,000 | 332 | 17,663 |
| 130,000 | 512 | 18,175 |
| 150,000 | 103 | 18,278 |
| 160,000 | 94 | 18,372 |
| 170,000 | 112 | 18,484 |
| 10,000,000 | 385 | 18,869 |

```dax


--  EARLIER is superseded by a careful usage of variables.

--  It is a best practice to avoid using EARLIER to make the code easier

--  to author and maintain.

EVALUATE

ADDCOLUMNS (

    VALUES ( Customer[Yearly Income] ),

    "Customers", CALCULATE ( COUNTROWS ( Customer ) ),

    "RT Customers",

        VAR CurrentYearlyIncome = Customer[Yearly Income]

        RETURN

        COUNTROWS (

            FILTER (

                Customer,

                Customer[Yearly Income] <= CurrentYearlyIncome 

            )

        )

)

ORDER BY [Yearly Income]

```

| Yearly Income | Customers | RT Customers |
| --- | --- | --- |
| 10,000 | 1,155 | 1,155 |
| 20,000 | 1,767 | 2,922 |
| 30,000 | 2,287 | 5,209 |
| 40,000 | 2,747 | 7,956 |
| 50,000 | 670 | 8,626 |
| 60,000 | 3,127 | 11,753 |
| 70,000 | 2,349 | 14,102 |
| 80,000 | 1,342 | 15,444 |
| 90,000 | 842 | 16,286 |
| 100,000 | 571 | 16,857 |
| 110,000 | 474 | 17,331 |
| 120,000 | 332 | 17,663 |
| 130,000 | 512 | 18,175 |
| 150,000 | 103 | 18,278 |
| 160,000 | 94 | 18,372 |
| 170,000 | 112 | 18,484 |
| 10,000,000 | 385 | 18,869 |

## Related articles

Learn more about EARLIER in the following articles:

- [**Variables in DAX**](https://www.sqlbi.com/articles/variables-in-dax/)

  In this article, you learn a new feature in DAX 2015: variables. The 2015 version of the DAX language has many new functions, but none of them is a game changer for the language as variables are. [» Read more](https://www.sqlbi.com/articles/variables-in-dax/)
- [**DAX coding style using variables**](https://www.sqlbi.com/articles/dax-coding-style-using-variables/)

  This article shows how variables in DAX can impact the coding style, simplifying a step-by-step approach and improving the readability of your code. [» Read more](https://www.sqlbi.com/articles/dax-coding-style-using-variables/)

## Related functions

Other related functions are:

- [[EARLIEST]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/earlier-function-dax](https://docs.microsoft.com/en-us/dax/earlier-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
