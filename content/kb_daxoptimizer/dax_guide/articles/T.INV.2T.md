---
title: "T.INV.2T"
function: "t-inv-2t"
category: "Statistical"
url: "https://dax.guide/t-inv-2t/"
source: "dax.guide"
重要度:
难度:
---

# T.INV.2T DAX Function (Statistical)

Returns the two-tailed inverse of the Student’s t-distribution.

## Syntax

T.INV.2T ( <Probability>, <Deg\_freedom> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Probability |  | The probability associated with the Student’s t-distribution. |
| Deg\_freedom |  | The number of degrees of freedom with which to characterize the distribution. |

## Return values

Scalar A single [decimal](https://dax.guide/dt/decimal/) value.

The two-tailed inverse of the Student’s t-distribution.

## Remarks

The two arguments cannot be negative.

[» 4 related functions](#alt)  

## Examples

```dax


--  T.INV    returns the left-tailed inverse of the Student's t-distribution

--  T.INV.2T returns the two-tailed inverse of the Student's t-distribution

--

--  https://en.wikipedia.org/wiki/Student%27s_t-distribution

DEFINE

    VAR DegFreedom      = 4.6

    VAR Percs           = GENERATESERIES ( 0.05, 0.99, 0.05 )

EVALUATE

SELECTCOLUMNS (

    Percs,

    "Probability %", [Value],

    "T.INV",   

        FORMAT (

            T.INV ( [Value], DegFreedom ),

            "#,0.00000"

        ),

    "T.INV.2T",

        FORMAT (

            T.INV.2T ( [Value], DegFreedom ),

            "#,0.00000"

        )

)

```

| Probability % | T.INV | T.INV.2T |
| --- | --- | --- |
| 5.0000000000% | -2.01505 | 2.57058 |
| 10.0000000000% | -1.47588 | 2.01505 |
| 15.0000000000% | -1.15577 | 1.69936 |
| 20.0000000000% | -0.91954 | 1.47588 |
| 25.0000000000% | -0.72669 | 1.30095 |
| 30.0000000000% | -0.55943 | 1.15577 |
| 35.0000000000% | -0.40823 | 1.03055 |
| 40.0000000000% | -0.26718 | 0.91954 |
| 45.0000000000% | -0.13218 | 0.81909 |
| 50.0000000000% | 0.00000 | 0.72669 |
| 55.0000000000% | 0.13218 | 0.64057 |
| 60.0000000000% | 0.26718 | 0.55943 |
| 65.0000000000% | 0.40823 | 0.48225 |
| 70.0000000000% | 0.55943 | 0.40823 |
| 75.0000000000% | 0.72669 | 0.33672 |
| 80.0000000000% | 0.91954 | 0.26718 |
| 85.0000000000% | 1.15577 | 0.19914 |
| 90.0000000000% | 1.47588 | 0.13218 |
| 95.0000000000% | 2.01505 | 0.06591 |

## Related functions

Other related functions are:

- [[T.DIST]]
- [[T.DIST.2T]]
- [[T.DIST.RT]]
- [[T.INV]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/t-inv-2t-dax](https://docs.microsoft.com/en-us/dax/t-inv-2t-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
