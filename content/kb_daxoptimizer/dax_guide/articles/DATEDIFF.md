---
title: "DATEDIFF"
function: "datediff"
category: "Date and Time"
url: "https://dax.guide/datediff/"
source: "dax.guide"
重要度:
难度:
---

# DATEDIFF DAX Function (Date and Time)

Returns the number of units (unit specified in Interval) between the input two dates.

## Syntax

DATEDIFF ( <Date1>, <Date2>, <Interval> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Date1 |  | A date in datetime format that represents the start date. |
| Date2 |  | A date in datetime format that represents the end date. |
| Interval |  | The unit that will be used to calculate, between the two dates. It can be [[SECOND]], [[MINUTE]], [[HOUR]], [[DAY]], WEEK, [[MONTH]], [[QUARTER]], [[YEAR]]. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

The count of interval boundaries crossed between two dates.

## Remarks

The result is positive if Date2 is larger than Date1.  
The result is negative if Date1 is larger than Date2.

Previous versions of the DAX engine (earlier than 2018) could provide an error if Date1 is larger than Date2.

[» 1 related article](#articles)  

## Examples

```dax


--  DATEDIFF computes the delta between two dates, using different units of measure

--  YEAFRAC returns the delta as a fraction (in years) 

EVALUATE

VAR StartDate =  DATE ( 2011, 01, 01 )

VAR EndDate =    DATE ( 2012, 12, 15 )

RETURN

    { 

        ( "DATEDIFF Year",     DATEDIFF ( StartDate, EndDate, YEAR ) ),

        ( "DATEDIFF Quarter",  DATEDIFF ( StartDate, EndDate, QUARTER ) ),

        ( "DATEDIFF Month",    DATEDIFF ( StartDate, EndDate, MONTH ) ),

        ( "DATEDIFF Day",      DATEDIFF ( StartDate, EndDate, DAY ) ),

        ( "Subtraction-1",     INT ( EndDate - StartDate ) ),

        ( "Subtraction-2",     CONVERT ( EndDate - StartDate, INTEGER ) ),

        ( "YEARFRAC",          YEARFRAC ( StartDate, EndDate ) )

    }    

```

| Value1 | Value2 |
| --- | --- |
| DATEDIFF Year | 1.00 |
| DATEDIFF Quarter | 7.00 |
| DATEDIFF Month | 23.00 |
| DATEDIFF Day | 714.00 |
| Subtraction-1 | 714.00 |
| Subtraction-2 | 714.00 |
| YEARFRAC | 1.96 |

## Related articles

Learn more about DATEDIFF in the following articles:

- [**DAX and semantic models announcements at the Fabric Conference 2025**](https://www.sqlbi.com/blog/marco/2025/04/04/dax-and-semantic-models-announcements-at-the-fabric-conference-2025/)

  I usually do not write about announcements and new features until we have had time to try and test them in the real world. However, there are always exceptions, and some of the announcements at the Microsoft Fabric Conference 2025 fall into this category because I have worked with them enough to provide hands-on feedback. In short, these are the topics I am covering in this blog post: Direct Lake and Import mode Calendars in DAX User Defined Functions (UDF) in DAX Direct Lake and Import mode One year ago, after two conferences where the announcement of Direct Lake was… [» Read more](https://www.sqlbi.com/blog/marco/2025/04/04/dax-and-semantic-models-announcements-at-the-fabric-conference-2025/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Ed Hansberry

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/datediff-function-dax](https://docs.microsoft.com/en-us/dax/datediff-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
