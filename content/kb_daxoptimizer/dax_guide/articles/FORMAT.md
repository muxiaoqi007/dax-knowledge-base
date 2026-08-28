---
title: "FORMAT"
function: "format"
category: "Text"
url: "https://dax.guide/format/"
source: "dax.guide"
重要度:
难度:
---

# FORMAT DAX Function (Text)

Converts a value to text in the specified number format.

## Syntax

FORMAT ( <Value>, <Format> [, <LocaleName>] )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Value |  | A number, or a formula that evaluates to a numeric value. |
| Format |  | A number format that you specify. |
| LocaleName | Optional | A locale name used when formatting the value. |

## Return values

Scalar A single [string](https://dax.guide/dt/string/) value.

A string containing **Value** formatted as defined by **Format** string.  
The result is always a string, even when the value is blank.

## Remarks

For information on how to use the **Format** string parameter:

- **Numbers**: Use [predefined numeric formats](https://docs.microsoft.com/en-us/dax/pre-defined-numeric-formats-for-the-format-function) or create [custom numeric formats](https://docs.microsoft.com/en-us/dax/custom-numeric-formats-for-the-format-function).
- **Dates and times**: Use [predefined date/time formats](https://docs.microsoft.com/en-us/dax/pre-defined-date-and-time-formats-for-the-format-function) or create [user-defined date/time formats](https://docs.microsoft.com/en-us/dax/custom-date-and-time-formats-for-the-format-function).

The format strings supported as an argument to the DAX FORMAT function are based on the [format strings](https://learn.microsoft.com/en-us/dax/format-function-dax#predefined-numeric-formats) used by Visual Basic (OLE Automation), not on the format strings used by the .NET Framework. Therefore, you might get unexpected results or an error if the argument does not match any defined format strings. For example, “p” as an abbreviation for “Percent” is not supported. Strings that you provide as an argument to the FORMAT function that are not included in the list of predefined format strings are handled as part of a custom format string, or as a string literal.

In order to make sure a literal is not interpreted as a format string, embed the literal between double quotes as in the examples.

The name of weekdays and months depends on the international settings of the database (or Power BI Desktop file).

The optional LocalName argument was introduced in 2022, and it is not available in SQL Server Analysis Services (SSAS) version earlier than 2022. The result of [[USERCULTURE]] can be used for the LocalName argument in order to use the culture from the user’s browser settings or operating system.

[» 4 related articles](#articles)  
[» 2 related functions](#alt)  

## Examples

```dax


--  FORMAT is a formatting function that formats a value based

--  on a format string.

--  Use a backslash (\) to display the next character 

--  in the format string.

EVALUATE

{

    ( "Percent",      FORMAT (                0.742, "Percent" )        ),

    ( "Currency (1)", FORMAT (             1234.567, "$#,0.00" )        ),

    ( "Currency (2)", FORMAT (             1234.567, """US$"" #,0.00" ) ),

    ( "Date (1)",     FORMAT ( DATE ( 2019, 3, 28 ), "yyyy-mm-dd" )     ),

    ( "Date (2)",     FORMAT ( DATE ( 2019, 3, 28 ), "m/d/yy" )         ),

    ( "Date (Q)",     FORMAT ( DATE ( 2019, 3, 28 ), "\QQ yyyy" )       )

}

```

| Value1 | Value2 |
| --- | --- |
| Percent | 74.20% |
| Currency (1) | $1,234.57 |
| Currency (2) | US$ 1,234.57 |
| Date (1) | 2019-03-28 |
| Date (2) | 3/28/19 |
| Date (Q) | Q1 2019 |

```dax


--  FORMAT accepts a set of predefined format strings to format numbers

EVALUATE

{

    ( "General Number",  FORMAT( 12345.67, "General Number" )  ),

    ( "Currency"      ,  FORMAT( 12345.67, "Currency"       )  ),

    ( "Fixed"         ,  FORMAT( 12345.67, "Fixed"          )  ),

    ( "Standard"      ,  FORMAT( 12345.67, "Standard"       )  ),

    ( "Percent"       ,  FORMAT( 12345.67, "Percent"        )  ),

    ( "Scientific"    ,  FORMAT( 12345.67, "Scientific"     )  ),

    ( "True/False"    ,  FORMAT( TRUE,     "True/False"     )  ),

    ( "On/Off"        ,  FORMAT( FALSE,    "On/Off"         )  ),

    ( "Yes/No"        ,  FORMAT( TRUE,     "Yes/No"         )  )

}

```

| Value1 | Value2 |
| --- | --- |
| General Number | 12345.67 |
| Currency | $12,345.67 |
| Fixed | 12345.67 |
| Standard | 12,345.67 |
| Percent | 1234567.00% |
| Scientific | 1.23E+04 |
| True/False | True |
| On/Off | Off |
| Yes/No | Yes |

```dax


--  Regardless of the format string, if the value argument is

--  BLANK, FORMAT returns an empty string

EVALUATE

{

    ( "General Number",  FORMAT ( BLANK (), "General Number" ) ),

    ( "Currency"      ,  FORMAT ( BLANK (), "Currency"       ) ),

    ( "Fixed"         ,  FORMAT ( BLANK (), "Fixed"          ) ),

    ( "Standard"      ,  FORMAT ( BLANK (), "Standard"       ) ),

    ( "Percent"       ,  FORMAT ( BLANK (), "Percent"        ) ),

    ( "Scientific"    ,  FORMAT ( BLANK (), "Scientific"     ) ),

    ( "True/False"    ,  FORMAT ( BLANK (), "True/False"     ) ),

    ( "On/Off"        ,  FORMAT ( BLANK (), "On/Off"         ) ),

    ( "Yes/No"        ,  FORMAT ( BLANK (), "Yes/No"         ) )

}

```

| Value1 | Value2 |
| --- | --- |
| General Number |  |
| Currency |  |
| Fixed |  |
| Standard |  |
| Percent |  |
| Scientific |  |
| True/False |  |
| On/Off |  |
| Yes/No |  |

```dax


--  Custom numeric format string can have up to 3 sections:

--      One section   : applied to all the values

--      Two sections  : first for positive and zero, second for negative

--      Three sections: first for positive, second for negative, third for zero

EVALUATE

{

    ( "Positive",  FORMAT (  1980.126, "#,0.00" ) ),

    ( "Negative",  FORMAT ( -1980.1  , "#,0.00" ) ),

    ( "Zero",      FORMAT (     0    , "#,0.00" ) ),

    ( "Blank",     FORMAT ( BLANK () , "#,0.00" ) )

}



EVALUATE

{

    ( "Positive",  FORMAT (  1980.126, "#,0.00;(#,0.00)" ) ),

    ( "Negative",  FORMAT ( -1980.1  , "#,0.00;(#,0.00)" ) ),

    ( "Zero",      FORMAT (     0    , "#,0.00;(#,0.00)" ) ),

    ( "Blank",     FORMAT ( BLANK () , "#,0.00;(#,0.00)" ) )

}



EVALUATE

{

    ( "Positive",  FORMAT (  1980.12, "#,#.##;(#,#.##);-" ) ),

    ( "Negative",  FORMAT ( -1980.12, "#,#.##;(#,#.##);-" ) ),

    ( "Zero",      FORMAT (     0   , "#,#.##;(#,#.##);-" ) ),

    ( "Blank",     FORMAT ( BLANK (), "#,#.##;(#,#.##);-" ) )

}



```

| Value1 | Value2 |
| --- | --- |
| Positive | 1,980.13 |
| Negative | -1,980.10 |
| Zero | 0.00 |
| Blank |  |

| Value1 | Value2 |
| --- | --- |
| Positive | 1,980.13 |
| Negative | (1,980.10) |
| Zero | 0.00 |
| Blank |  |

| Value1 | Value2 |
| --- | --- |
| Positive | 1,980.12 |
| Negative | (1,980.12) |
| Zero | – |
| Blank |  |

```dax


--  FORMAT accepts a set of predefined format strings for Date/Time

DEFINE 

    VAR D  = DATE ( 2021, 1, 2 )

    VAR T  = TIME ( 15, 30, 0 )

    VAR DT = D + T

EVALUATE

{

    ( "General Date",  FORMAT ( DT, "General Date" ) ),

    ( "Long Date"   ,  FORMAT ( DT, "Long Date"    ) ),

    ( "Medium Date" ,  FORMAT ( DT, "Medium Date"  ) ),

    ( "Short Date"  ,  FORMAT ( DT, "Short Date"   ) ),

    ( "Long Time"   ,  FORMAT ( DT, "Long Time"    ) ),

    ( "Medium Time" ,  FORMAT ( DT, "Medium Time"  ) ),

    ( "Short Time"  ,  FORMAT ( DT, "Short Time"   ) )

}

```

| Value1 | Value2 |
| --- | --- |
| General Date | 1/2/2021 3:30:00 PM |
| Long Date | Saturday, January 2, 2021 |
| Medium Date | 02-Jan-21 |
| Short Date | 1/2/2021 |
| Long Time | 3:30:00 PM |
| Medium Time | 03:30 PM |
| Short Time | 15:30 |

```dax


--  You can use simple date formatting composing the format string 

--  with dd, mm, yyyy, hh, mm and ss.

--  The "c" format string adapts to the value, showing what is relevant

DEFINE 

    VAR D  = DATE ( 2020, 1, 2 )

    VAR T  = TIME ( 15, 30, 0 )

    VAR DT = D + T

EVALUATE

{

    ( "dd/mm/yyyy"   ,  FORMAT ( DT, "dd/mm/yyyy" ) ),

    ( "hh:nn:ss"     ,  FORMAT ( DT, "hh:nn:ss"   ) ),  -- "nn" for minutes

    ( "hh:mm:ss"     ,  FORMAT ( DT, "hh:mm:ss"   ) ),  -- "mm" works too, if hh before

    ( "c (full)"     ,  FORMAT ( DT, "c"          ) ),

    ( "c (Date only)",  FORMAT ( D,  "c"          ) ),

    ( "c (Time only)",  FORMAT ( T,  "c"          ) )

}

```

| Value1 | Value2 |
| --- | --- |
| dd/mm/yyyy | 02/01/2020 |
| hh:nn:ss | 15:30:00 |
| hh:mm:ss | 15:30:00 |
| c (full) | 1/2/2020 3:30:00 PM |
| c (Date only) | 1/2/2020 |
| c (Time only) | 3:30:00 PM |

```dax


--  You need two FORMAT functions for a duration longer than one day

--  The first FORMAT returns the number of days,

--  the second FORMAT displays the duration in hours/minutes/seconds

DEFINE

    VAR StartEvent = DATE ( 2020, 1, 2 ) + TIME ( 15, 30, 0 )

    VAR EndEvent = DATE ( 2020, 1, 4 ) + TIME ( 6, 18, 42 )

    VAR DurationEvent = EndEvent - StartEvent

EVALUATE

{

    FORMAT ( INT ( DurationEvent ), "0:" ) & FORMAT ( DurationEvent, "hh:nn:ss" )

}

```

| Value |
| --- |
| 1:14:48:42 |

```dax


--  The "m" format string offers many options, depending on the 

--  number of consecutive m

DEFINE 

    VAR D = DATE ( 2020, 1, 2 )

EVALUATE

{

    ( "m"      ,  FORMAT ( D, "m"      ) ),    -- 1 or 2 digits 

    ( "mm"     ,  FORMAT ( D, "mm"     ) ),    -- always 2 digits

    ( "mmm"    ,  FORMAT ( D, "mmm"    ) ),    -- short day of month

    ( "mmmm"   ,  FORMAT ( D, "mmmm"   ) )     -- long day of month

}



```

| Value1 | Value2 |
| --- | --- |
| m | 1 |
| mm | 01 |
| mmm | Jan |
| mmmm | January |

```dax


--  The "d" format string offers many options, depending on the 

--  number of consecutive d

DEFINE 

    VAR D = DATE ( 2020, 1, 9 )

EVALUATE

{

    ( "d"      ,  FORMAT ( D, "d"      ) ),    -- 1 or 2 digits

    ( "dd"     ,  FORMAT ( D, "dd"     ) ),    -- always 2 digits

    ( "ddd"    ,  FORMAT ( D, "ddd"    ) ),    -- short day of week

    ( "dddd"   ,  FORMAT ( D, "dddd"   ) ),    -- long day of week

    ( "ddddd"  ,  FORMAT ( D, "ddddd"  ) ),    -- short date

    ( "dddddd" ,  FORMAT ( D, "dddddd" ) )     -- long date

}

```

| Value1 | Value2 |
| --- | --- |
| d | 9 |
| dd | 09 |
| ddd | Thu |
| dddd | Thursday |
| ddddd | 1/9/2020 |
| dddddd | Thursday, January 9, 2020 |

```dax


-- The third optional argument specifies the locale identifier

EVALUATE {

    FORMAT( dt"2020-12-15T12:30:59", BLANK(), "en-US" ),

    FORMAT( dt"2020-12-15T12:30:59", BLANK(), "en-GB" ),  

    FORMAT( dt"2020-12-15T12:30:59", "mm/dd/yyyy", "en-GB" ),

    FORMAT( dt"2020-12-15T12:30:59", "dd mmmm yyyy", "en-US" ),

    FORMAT( dt"2020-12-15T12:30:59", "dd mmmm yyyy", "pt-BR" ),

    FORMAT( dt"2020-12-15T12:30:59", "dd mmmm yyyy", "ru-RU" ),

    FORMAT( 1234.567, "#,0.00", "en-US" ),  -- Use comma as thousand separator and dot as decimal separator

    FORMAT( 1234.567, "#,0.00", "de-DE" )   -- Use dot as thousand separator and comma as decimal separator

}

```

| Value |
| --- |
| 12/15/2020 12:30:59 PM |
| 15/12/2020 12:30:59 |
| 12/15/2020 |
| 15 December 2020 |
| 15 dezembro 2020 |
| 15 декабря 2020 |
| 1,234.57 |
| 1.234,57 |

```dax


-- Consecutive commas not followed by 0 before the decimal point

-- divide the number by 1,000 for each comma

EVALUATE

VAR BigNumber = 103456789102

RETURN

    {

        FORMAT ( BigNumber, "$#,0" ),         -- Units

        FORMAT ( BigNumber, "$#,0,.0#K" ),    -- Thousand

        FORMAT ( BigNumber, "$#,0,,.0#M" ),   -- Million

        FORMAT ( BigNumber, "$#,0,,,.0#B" ),  -- Billion

        FORMAT ( BigNumber, "$#,0,,,,.0#T" )  -- Trillion

    }



```

| Value |
| --- |
| $103,456,789,102 |
| $103,456,789.1K |
| $103,456.79M |
| $103.46B |
| $0.1T |

```dax


-- Dynamically select the format string

-- based on the order of magnitude of the value

DEFINE

    TABLE Numbers = { 103, 10345, 103456, 1034567, 103456789, 134897891, 103456789102, 103499789102, 1034567891023 }

    MEASURE Numbers[SkipThousands] =

        VAR CurrentNumber =

            SELECTEDVALUE ( Numbers[Value] )

        VAR LogRatio =

            IF ( CurrentNumber > 0, DIVIDE ( LOG ( CurrentNumber ), LOG ( 1000 ) ) )

        RETURN

            ROUNDDOWN ( DIVIDE ( LOG10 ( CurrentNumber ), 3 ), 0 )

    MEASURE Numbers[ValueFormatString] =

        SWITCH (

            [SkipThousands],

            0, "$#,0",        -- Integer number

            1, "$#,0,.0#K",   -- Thousand

            2, "$#,0,,.0#M",  -- Million

            3, "$#,0,,,.0#B", -- Billion

            "$#,0,,,,.0#T"    -- Trillion

        )

EVALUATE

ADDCOLUMNS (

    Numbers,

    "Formatted Value", FORMAT ( Numbers[Value], [ValueFormatString] ),

    "Format String", [ValueFormatString]

)



```

| Numbers[Value] | Formatted Value | Format String |
| --- | --- | --- |
| 103 | $103 | $#,0 |
| 10,345 | $10.35K | $#,0,.0#K |
| 103,456 | $103.46K | $#,0,.0#K |
| 1,034,567 | $1.03M | $#,0,,.0#M |
| 103,456,789 | $103.46M | $#,0,,.0#M |
| 134,897,891 | $134.9M | $#,0,,.0#M |
| 103,456,789,102 | $103.46B | $#,0,,,.0#B |
| 103,499,789,102 | $103.5B | $#,0,,,.0#B |
| 1,034,567,891,023 | $1.03T | $#,0,,,,.0#T |

## Related articles

Learn more about FORMAT in the following articles:

- [**Creating a simple date table in DAX**](https://www.sqlbi.com/articles/creating-a-simple-date-table-in-dax/)

  This article shows how to build a basic date table using a calculated table and DAX. [» Read more](https://www.sqlbi.com/articles/creating-a-simple-date-table-in-dax/)
- [**Rounding errors with different data types in DAX**](https://www.sqlbi.com/articles/rounding-errors-with-different-data-types-in-dax/)

  This article describes the possible rounding differences that can appear in DAX. They are related to the data types and the operation being performed: knowing these details helps you write more robust DAX formulas and avoid errors in comparisons. [» Read more](https://www.sqlbi.com/articles/rounding-errors-with-different-data-types-in-dax/)
- [**Improving data labels with format strings**](https://www.sqlbi.com/articles/improving-data-labels-with-format-strings/)

  This article describes the different approaches to format your DAX measures in Power BI semantic models using format custom and dynamic format strings. [» Read more](https://www.sqlbi.com/articles/improving-data-labels-with-format-strings/)
- [**Optional parameters in DAX user-defined functions**](https://www.sqlbi.com/articles/optional-parameters-in-dax-user-defined-functions/)

  This article describes how to define optional parameters in DAX user-defined functions and set default values for parameters not specified by the caller. [» Read more](https://www.sqlbi.com/articles/optional-parameters-in-dax-user-defined-functions/)

## Related functions

Other related functions are:

- [[FIXED]]
- [[USERCULTURE]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Daniel Otykier, Vahid Doustimajd,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/format-function-dax](https://docs.microsoft.com/en-us/dax/format-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
