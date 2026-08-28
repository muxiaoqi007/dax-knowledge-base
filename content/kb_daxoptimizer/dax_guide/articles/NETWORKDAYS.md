---
title: "NETWORKDAYS"
function: "networkdays"
category: "Date and Time"
url: "https://dax.guide/networkdays/"
source: "dax.guide"
重要度:
难度:
---

# NETWORKDAYS DAX Function (Date and Time)

Returns the number of whole workdays between two dates (inclusive) using parameters to indicate which and how many days are weekend days. Weekend days and any days that are specified as holidays are not considered as workdays.

## Syntax

NETWORKDAYS ( <start\_date>, <end\_date> [, <weekend>] [, <holidays>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| start\_date |  | A date that represents the start date. |
| end\_date |  | A date that represents the end date. |
| weekend | Optional | Indicates the days of the week that are weekend days and are not included in the number of whole working days between start\_date and end\_date. Weekend is a weekend number that specifies when weekends occur. Weekend number values indicate the following weekend days: 1 or omitted: Saturday, Sunday; 2: Sunday, Monday; 3: Monday, Tuesday; 4: Tuesday, Wednesday; 5; Wednesday, Thursday; 6: Thursday, Friday; 7: Friday, Saturday; 11: Sunday only; 12: Monday only; 13: Tuesday only; 14: Wednesday only; 15: Thursday only; 16: Friday only; 17: Saturday only. |
| holidays | Optional | A single column table of one or more dates that are to be excluded from the working day calendar. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

Returns the number of whole workdays between two dates (inclusive).

[» 1 related article](#articles)  

## Examples

```dax


-- Count the working days in May 2022 

-- ignoring any holiday

EVALUATE 

UNION ( 

    ROW ( 

        "Type", "Sat/Sun", 

        "Working Days", NETWORKDAYS ( 

            dt"2022-05-01", 

            dt"2022-05-31", 

            1               -- Ignore Saturday and Sunday

        )

    ),

    ROW ( 

        "Type", "Sunday", 

        "Working Days", NETWORKDAYS ( 

            dt"2022-05-01", 

            dt"2022-05-31", 

            11               -- Ignore only Sunday

        )

    ),

    ROW ( 

        "Type", "Sat/Sun + Memorial Day", 

        "Working Days", NETWORKDAYS ( 

            dt"2022-05-01", 

            dt"2022-05-31", 

            1,                 -- Ignore Saturday and Sunday

            { dt"2022-05-30" } -- Ignore Memorial Day (US)

        )

    )

)

```

| Type | Working Days |
| --- | --- |
| Sat/Sun | 22 |
| Sunday | 26 |
| Sat/Sun + Memorial Day | 21 |

```dax


DEFINE

    VAR HolidaysDates =

        CALCULATETABLE ( 

            DISTINCT ( 'Date'[Date] ), 

            'Date'[Is Holiday] = 1 

        )

    VAR SalesDecember =

        CALCULATETABLE (

            SUMMARIZE (

                Sales,

                Sales[Order Number],

                Sales[Order Date],

                Sales[Delivery Date]

            ),

            Sales[Order Date] = dt"2008-12-23"

        )



EVALUATE

TOPN (

    10,

    SELECTCOLUMNS (

        SalesDecember,

        Sales[Order Number],

        Sales[Order Date],

        Sales[Delivery Date],

        "Delivery Working Days", NETWORKDAYS ( 

            Sales[Order Date], 

            Sales[Delivery Date], 

            1,

            HolidaysDates

        ) - 1

    )

)

```

| Order Number | Order Date | Delivery Date | Delivery Working Days |
| --- | --- | --- | --- |
| 200812235CS669 | 2008-12-23 | 2008-12-31 | 5 |
| 200812235CS669 | 2008-12-23 | 2008-12-30 | 4 |
| 200812233CS669 | 2008-12-23 | 2008-12-30 | 4 |
| 200812233CS669 | 2008-12-23 | 2008-12-29 | 3 |
| 200812234CS669 | 2008-12-23 | 2008-12-29 | 3 |
| 200812234CS669 | 2008-12-23 | 2009-01-03 | 6 |
| 200812234CS669 | 2008-12-23 | 2009-01-02 | 6 |
| 200812233CS669 | 2008-12-23 | 2008-12-31 | 5 |
| 200812238CS669 | 2008-12-23 | 2009-01-03 | 6 |
| 200812238CS669 | 2008-12-23 | 2009-01-02 | 6 |

## Related articles

Learn more about NETWORKDAYS in the following articles:

- [**Rolling average with working days in DAX**](https://www.sqlbi.com/articles/rolling-average-with-working-days-in-dax/)

  Rolling averages are a very common calculation used to smooth out charts. This article shows how to compute a rolling average taking into account only the working days. [» Read more](https://www.sqlbi.com/articles/rolling-average-with-working-days-in-dax/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://learn.microsoft.com/dax/networkdays-dax](https://learn.microsoft.com/dax/networkdays-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
