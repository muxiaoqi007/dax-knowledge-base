---
title: "CONTAINSSTRINGEXACT"
function: "containsstringexact"
category: "Information"
url: "https://dax.guide/containsstringexact/"
source: "dax.guide"
重要度:
难度:
---

# CONTAINSSTRINGEXACT DAX Function (Information)

Returns TRUE if one text string contains another text string. CONTAINSSTRINGEXACT is case-sensitive and accent-sensitive.

## Syntax

CONTAINSSTRINGEXACT ( <WithinText>, <FindText> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| WithinText |  | The text in which you want to search for FindText. |
| FindText |  | The text you want to find. Wildcard characters are not allowed. |

## Return values

Scalar A single [boolean](https://dax.guide/dt/boolean/) value.

A value of TRUE if the string WithinText contains the string FindText – the comparison is case-sensitive.

## Remarks

The CONTAINSSTRINGEXACT function does not accept wildcards to perform the search, whereas [[CONTAINSSTRING]] accepts wildcards and is not case-sensitive.

CONTAINSSTRINGEXACT internally uses the [[FIND]] function.

[» 2 related articles](#articles)  
[» 1 related function](#alt)  

## Examples

```dax


-- CONTAINSSTRINGEXACT is syntax sugar for FIND

-- The following two queries return the same result

EVALUATE

FILTER ( 

    ALL ( 'Product'[Color] ), 

    CONTAINSSTRINGEXACT ( 'Product'[Color], "Grey" ) 

)



EVALUATE

FILTER (

    ALL ( 'Product'[Color] ),

    FIND ( "Grey", 'Product'[Color], 1, 0 ) > 0

)

```

| Color |
| --- |
| Grey |
| Silver Grey |

| Color |
| --- |
| Grey |
| Silver Grey |

```dax


--  CONTAINSSTRING checks whether a string contains another string, using

--  case-insensitive match. Therefore, "RED" = "red".

--  CONTAINSSTRINGEXACT uses case-sensitive matching

DEFINE

    MEASURE Sales[RED Prods (case insensitive)] =

        COUNTROWS (

            FILTER ( Product, CONTAINSSTRING ( 'Product'[Product Name], "red" ) )

        )

    MEASURE Sales[RED Prods (case sensitive)] =

        COUNTROWS (

            FILTER ( Product, CONTAINSSTRINGEXACT ( 'Product'[Product Name], "red" ) )

        )

EVALUATE

FILTER ( 

    SUMMARIZECOLUMNS (

        Product[Product Name],

        "RED Prods (case sensitive)", [RED Prods (case sensitive)],

        "RED Prods (case insensitive)", [RED Prods (case insensitive)]

    ), 

    OR ( 

        [RED Prods (case insensitive)] <> [RED Prods (case sensitive)],

        [RED Prods (case sensitive)] = 1 

    ) 

)

ORDER BY 

    [RED Prods (case sensitive)] DESC

```

| Product Name | RED Prods (case sensitive) | RED Prods (case insensitive) |
| --- | --- | --- |
| Proseware 23ppm Laser Printer with Wireless and Wired Network Interfaces M680 White | 1 | 1 |
| The Phone Company Touch Screen Phones Infrared M901 Black | 1 | 1 |
| The Phone Company Touch Screen Phones Infrared M901 Grey | 1 | 1 |
| Proseware 23ppm Laser Printer with Wireless and Wired Network Interfaces M680 Black | 1 | 1 |
| The Phone Company Touch Screen Phones Infrared M901 Gold | 1 | 1 |
| Contoso Touch Screen Phones Infrared M901 Black | 1 | 1 |
| Proseware 23ppm Laser Printer with Wireless and Wired Network Interfaces M680 Grey | 1 | 1 |
| NT Bluetooth Active Headphones E202 Red | (Blank) | 1 |
| NT Wireless Bluetooth Stereo Headphones M402 Red | (Blank) | 1 |
| NT Wireless Transmitter and Bluetooth Headphones M150 Red | (Blank) | 1 |
| WWI Wireless Transmitter and Bluetooth Headphones X250 Red | (Blank) | 1 |
| Fabrikam Laptop14.1W M4180 Red | (Blank) | 1 |
| Fabrikam Laptop14.1 E4101 Red | (Blank) | 1 |
| Fabrikam Laptop8.9 E8002 Red | (Blank) | 1 |
| Fabrikam Laptop12 M2002 Red | (Blank) | 1 |
| Fabrikam Laptop13.3 M3000 Red | (Blank) | 1 |
| Fabrikam Laptop13.3W M3080 Red | (Blank) | 1 |
| Adventure Works Laptop19W X1980 Red | (Blank) | 1 |
| Adventure Works Laptop15 M1501 Red | (Blank) | 1 |
| Adventure Works Laptop12 M1201 Red | (Blank) | 1 |
| Adventure Works Laptop16 M1601 Red | (Blank) | 1 |
| Adventure Works Laptop15.4W M1548 Red | (Blank) | 1 |
| Adventure Works Laptop8.9 E0890 Red | (Blank) | 1 |
| WWI Desktop PC1.60 E1600 Red | (Blank) | 1 |
| SV 40GB USB2.0 Portable Hard Disk E400 Red | (Blank) | 1 |
| SV 80GB USB2.0 Portable Hard Disk E500 Red | (Blank) | 1 |
| Contoso Mini Battery Charger Kit E320 Red | (Blank) | 1 |
| SV DVD 48 DVD Storage Binder M50 Red | (Blank) | 1 |
| SV DVD 58 DVD Storage Binder M55 Red | (Blank) | 1 |
| SV DVD 38 DVD Storage Binder E25 Red | (Blank) | 1 |
| SV DVD 60 DVD Storage Binder L20 Red | (Blank) | 1 |
| SV DVD 55DVD Storage Binder M56 Red | (Blank) | 1 |
| Contoso DVD 48 DVD Storage Binder M50 Red | (Blank) | 1 |
| Contoso DVD 58 DVD Storage Binder M55 Red | (Blank) | 1 |
| Contoso DVD 38 DVD Storage Binder E25 Red | (Blank) | 1 |
| Contoso DVD 60 DVD Storage Binder L20 Red | (Blank) | 1 |
| Contoso DVD 55DVD Storage Binder M56 Red | (Blank) | 1 |
| MGS Hand Games for kids E300 Red | (Blank) | 1 |
| MGS Hand Games for students E400 Red | (Blank) | 1 |
| MGS Hand Games men M300 Red | (Blank) | 1 |
| MGS Hand Games women M400 Red | (Blank) | 1 |
| MGS Hand Games for 12-16 boys E600 Red | (Blank) | 1 |
| MGS Hand Games for Office worker L299 Red | (Blank) | 1 |
| SV Hand Games for kids E30 Red | (Blank) | 1 |
| SV Hand Games for students E40 Red | (Blank) | 1 |
| SV Hand Games men M30 Red | (Blank) | 1 |
| SV Hand Games women M40 Red | (Blank) | 1 |
| SV Hand Games for Office worker L28 Red | (Blank) | 1 |
| Contoso Washer & Dryer 27in L270 Red | (Blank) | 1 |
| Contoso Washer & Dryer 25.5in M255 Red | (Blank) | 1 |
| Contoso Washer & Dryer 24in M240 Red | (Blank) | 1 |
| Contoso Washer & Dryer 21in E210 Red | (Blank) | 1 |
| Contoso Washer & Dryer 15.5in E155 Red | (Blank) | 1 |
| Litware Refrigerator 19CuFt M760 Red | (Blank) | 1 |
| Litware Refrigerator 9.7CuFt M560 Red | (Blank) | 1 |
| Fabrikam Microwave 1.5CuFt X1100 Red | (Blank) | 1 |
| Fabrikam Microwave 2.2CuFt M1250 Red | (Blank) | 1 |
| Fabrikam Microwave 1.6CuFt M1250 Red | (Blank) | 1 |
| Fabrikam Microwave 1.0CuFt E1100 Red | (Blank) | 1 |
| Fabrikam Microwave 0.9CuFt E0900 Red | (Blank) | 1 |
| Fabrikam Microwave 0.8CuFt E0800 Red | (Blank) | 1 |
| Litware Microwave 1.5CuFt X110 Red | (Blank) | 1 |
| Litware Microwave 2.2CuFt M125 Red | (Blank) | 1 |
| Litware Microwave 1.6CuFt M125 Red | (Blank) | 1 |
| Litware Microwave 1.0CuFt E110 Red | (Blank) | 1 |
| Litware Microwave 0.9CuFt E090 Red | (Blank) | 1 |
| Litware Microwave 0.8CuFt E080 Red | (Blank) | 1 |
| Contoso Microwave 1.5CuFt X0110 Red | (Blank) | 1 |
| Contoso Microwave 2.2CuFt M0125 Red | (Blank) | 1 |
| Contoso Microwave 1.6CuFt M0125 Red | (Blank) | 1 |
| Contoso Microwave 1.0CuFt E0110 Red | (Blank) | 1 |
| Contoso Microwave 0.9CuFt E0090 Red | (Blank) | 1 |
| Contoso Microwave 0.8CuFt E0080 Red | (Blank) | 1 |
| Contoso Water Heater 7.2GPM X1800 Red | (Blank) | 1 |
| Contoso Water Heater 4.3GPM M1250 Red | (Blank) | 1 |
| Contoso Water Heater 4.0GPM M1250 Red | (Blank) | 1 |
| Contoso Water Heater 2.6GPM E0900 Red | (Blank) | 1 |
| Contoso Water Heater 1.5GPM E0800 Red | (Blank) | 1 |
| Contoso Air conditioner 25000BTU L1672 Red | (Blank) | 1 |
| Contoso Air conditioner 12000BTU M0640 Red | (Blank) | 1 |
| Contoso Air conditioner 10000BTU M0490 Red | (Blank) | 1 |
| Contoso Air conditioner 8000BTU M0320 Red | (Blank) | 1 |
| Contoso Air conditioner 7000BTU E0260 Red | (Blank) | 1 |
| Contoso Air conditioner 6000BTU E0180 Red | (Blank) | 1 |
| Contoso Air conditioner 5200BTU E0100 Red | (Blank) | 1 |
| Proseware Air conditioner 25000BTU L167 Red | (Blank) | 1 |
| Proseware Air conditioner 12000BTU M640 Red | (Blank) | 1 |
| Proseware Air conditioner 10000BTU M490 Red | (Blank) | 1 |
| Proseware Air conditioner 8000BTU M320 Red | (Blank) | 1 |
| Proseware Air conditioner 7000BTU E260 Red | (Blank) | 1 |
| Proseware Air conditioner 6000BTU E180 Red | (Blank) | 1 |
| Proseware Air conditioner 5200BTU E100 Red | (Blank) | 1 |
| Litware USB Foam Fan E1301 Red | (Blank) | 1 |
| Litware 180 CFM Vertical Discharge Fan X450 Red | (Blank) | 1 |
| Litware USB Durable Desk Soft Fan E1401 Red | (Blank) | 1 |
| Litware 80mm LED Dual PCI Slot Fan E1501 Red | (Blank) | 1 |
| Cigarette Lighter Adapter for Contoso Phones E110 Red | (Blank) | 1 |
| Contoso Touch Stylus Pen E150 Red | (Blank) | 1 |
| Contoso Bluetooth Active Headphones L15 Red | (Blank) | 1 |
| SV Hand Games for 12-16 boys E60 Red | (Blank) | 1 |
| Contoso 2G MP3 Player E200 Red | (Blank) | 1 |
| Contoso 8GB Super-Slim MP3/Video Player M800 Red | (Blank) | 1 |
| Contoso 16GB Mp5 Player M1600 Red | (Blank) | 1 |
| Contoso 32GB Video MP3 Player M3200 Red | (Blank) | 1 |
| WWI 4GB Video Recording Pen X200 Red | (Blank) | 1 |
| WWI 1GB Digital Voice Recorder Pen E100 Red | (Blank) | 1 |

## Related articles

Learn more about CONTAINSSTRINGEXACT in the following articles:

- [**From SQL to DAX: String Comparison**](https://www.sqlbi.com/articles/from-sql-to-dax-string-comparison/)

  In DAX string comparison requires you more attention than in SQL, for several reasons: DAX doesn’t offer the same set of features you have in SQL, a few text comparison functions in DAX are only case-sensitive and others only case-insensitive,… [» Read more](https://www.sqlbi.com/articles/from-sql-to-dax-string-comparison/)
- [**Optimizing text search in DAX**](https://www.sqlbi.com/articles/optimizing-text-search-in-dax/)

  This article describes how to optimize a text search operation in DAX. This technique can improve the performance of Power BI reports that use the contains condition in the filter pane or the filter mode of the Smart Filter Pro custom visual. [» Read more](https://www.sqlbi.com/articles/optimizing-text-search-in-dax/)

## Related functions

Other related functions are:

- [[CONTAINSSTRING]]

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Jes Hansen, Naji El Kotob, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/containsstringexact-function-dax](https://docs.microsoft.com/en-us/dax/containsstringexact-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
