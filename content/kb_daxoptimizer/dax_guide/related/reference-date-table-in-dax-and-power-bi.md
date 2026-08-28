---
title: "Reference Date Table in DAX and Power BI"
url: "https://www.sqlbi.com/articles/reference-date-table-in-dax-and-power-bi"
published: "2023-08-01"
updated: 
source: "sqlbi.com"
---

# Reference Date Table in DAX and Power BI

> 发布：2023-08-01

**UPDATE 2023-08-01**: You can automatically generate a Date table by using [Bravo for Power BI](https://bravo.bi), which has also a template system that allows you to customize the Date table you want. Read more in the documentation of the [Manage Dates](https://docs.sqlbi.com/bravo/features/manage-dates/) feature.

[![](https://cdn.sqlbi.com/wp-content/uploads/DAX-Date-Template-logo-text-yellow.png)](https://www.sqlbi.com/tools/dax-date-template)

## Why a reference Date table

The Auto Date/Time feature available in Power BI presents several limitations:

- It has a fixed set of rows.
- It does not handle fiscal years.
- It does not include weeks.
- It cannot be shared across different tables in the same data model.

Usually, it is necessary to disable the feature and to create a custom Date table. This task is repetitive and time consuming. Creating new Power BI models starting from a Power BI template containing a fully-featured Date table spares the user from writing the required DAX expression – as well as in setting the necessary properties to define display format, hierarchies, and visibility of the columns required in reports and calculations.

## What a Power BI template file (PBIT) is

A file with a .PBIT extension is a Power BI template file, which does not contain data and imports / generates data when it is opened. Any Power BI file can be saved as a template file. All the tables included in the data model are saved as empty tables. They are populated when the template is opened, reading data from the data source.

A common use of Power BI template files is to distribute a standard data model that can be connected to a data source with a certain parameter, changing the filters of the data loaded in memory. For the reference Date table, the Power BI template contains a calculated table that is populated when the file is opened. This way, the template file is smaller and can be used as an “empty” file to create a new data model in Power BI – starting with a standard Date table that will be connected to other tables containing date columns.  
[![](https://cdn.sqlbi.com/wp-content/uploads/ReferenceDateTable-01.png)](https://cdn.sqlbi.com/wp-content/uploads/ReferenceDateTable-01.png)

## How to use the reference Date table template

Open the “Date template.pbit” file to create a new Power BI project. The model contains a single calculated table named Date. Date contains all the days that exist within a range of years defined by two configuration parameters – FirstYear, and LastYear.  
[![](https://cdn.sqlbi.com/wp-content/uploads/ReferenceDateTable-02.png)](https://cdn.sqlbi.com/wp-content/uploads/ReferenceDateTable-02.png)

The initial section of the calculated table contains several parameters controlling how the Date table is generated. The columns are divided in several sections:

- Base date columns
  - Include basic information such as date, day of the week, day of the month
- Solar Calendar (prefix Calendar)
  - Columns for standard monthly calendar January-December
- Fiscal Monthly Calendar (prefix Fiscal)
  - Columns for fiscal monthly (Gregorian) calendar where a fiscal year starts on the first day of a month that is not January (see parameters)
- Fiscal Weekly Calendar (prefix FW)
  - Columns for fiscal weekly calendar where the year starts on a particular day, following one of the supported rules (see parameters)
  - Supports ISO, 4-4-5, 4-5-4, 5-4-4 weekly calendars
- Holidays and working days
  - Columns for holidays and working days, according to holidays in a country defined in the parameters

## Columns Selection

The final part of the DAX expression is a SELECTCOLUMN statement that includes all the columns used in the template. It is possible to customize the Date table by removing or commenting the unwanted columns from the [[SELECTCOLUMNS]] statement. The columns removed from the DAX calculated table are also removed from the Date table, removing related hierarchies too.  
[![](https://cdn.sqlbi.com/wp-content/uploads/ReferenceDateTable-03.png)](https://cdn.sqlbi.com/wp-content/uploads/ReferenceDateTable-03.png)  
It is suggested to remove the columns only as a final step. A column that is commented and then uncommented will appear in the Date table having lost any additional setting (hierarchy, visibility, format) that was previously applied.

Power BI shows a limited number of rows in its editor. Use a tool like DAX Editor to modify the DAX expression of the Date table. Use copy and paste to move the code between Power BI and the external editor.  
[![](https://cdn.sqlbi.com/wp-content/uploads/ReferenceDateTable-04.png)](https://cdn.sqlbi.com/wp-content/uploads/ReferenceDateTable-04.png)

## Columns Reference

The naming convention used for the column names is the following:

- Complete names with spaces (e.g. “Day of Month”) are visible columns. If the column is hidden, it is visible through a hierarchy level.
- Names in Pascal casing (e.g. “WeekDayNumber”) are hidden columns used for internal calculation or used to sort other columns.

## Base Date Columns

*The following examples are obtained and described using March 21, 2018 as a reference.*

- **Date**: 3/21/2018 *(Date)* – This is the date in Date data type
- **DateKey**: 20180321 *(Integer)* – Date in integer format YYYYMMDD
- **Day of Month**: 21 *(Integer)* – Day of the month
- **WeekDayNumber**: 3 *(Integer)* – Day of the week in numeric format, where 1 is the first day of the week (depends on configuration parameters)
- **Week Day**: Tue *(String)* – Name of the day of the week, three letters
- **Sequential365DayNumber**: 43151 *(Integer)* – Number of days since December 30, 1899, excluding February 29 (useful to compute moving annual total with 365 days)

## Solar Calendar (Calendar)

- **Calendar YearNumber**: 2018 *(Integer)* – Calendar year number
- **Calendar Year**: 2018 *(String)* – Calendar year, sorted by Calendar YearNumber
- **Calendar QuarterNumber**: 1 *(Integer)* – Calendar quarter number
- **Calendar Quarter**: Q1 *(String)* – Calendar quarter, sorted by Calendar QuarterNumber
- **Calendar YearQuarterNumber**: 8072 *(Integer)* – Sequential quarter number across years (= [Calendar YearNumber] \* 4 + [Calendar QuarterNumber] – 1)
- **Calendar Quarter Year**: Q1 2018 *(String)* – Quarter and year, sorted by Calendar YearQuarterNumber
- **Calendar MonthNumber**: 3 *(Integer)* – Calendar month number, where 1 is January
- **Calendar Month**: March *(String)* – Calendar month name, sorted by Calendar MonthNumber
- **Calendar YearMonthNumber**: 24218 *(Integer)* – Sequential month number across years (= [Calendar YearNumber] \* 12 + [Calendar MonthNumber] – 1)
- **Calendar Month Year**: Mar 2018 *(String)* – Month and year, sorted by Calendar YearMonthNumber
- **Calendar WeekNumber**: 12 *(Integer)* – Calendar week number
- **Calendar Week**: W12 *(String)* – Calendar week, sorted by Calendar WeekNumber
- **Calendar YearWeekNumber**: 6116 *(Integer)* – Sequential year number across years. A week always counts 7 days. When a week crosses over from December into January, same week number is used on both sides.
- **Calendar Week Year**: W12-2018 *(String)* – Calendar week and year, sorted by Calendar WeekYearOrder (a calendar week can have less than 7 days at start and end of year)
- **Calendar WeekYearOrder**: 201812 *(Integer)* – Calendar year and week in YYYYWW format (a calendar week can have less than 7 days at start and end of year)
- **Calendar RelativeWeekPos**: -8 *(Integer)* – Relative weeks compared to “TodayReference” (see parameters); negative values are for weeks before TodayReference, positive values are for weeks after TodayReference
- **Calendar RelativeMonthPos**: -2 *(Integer)* – Relative months compared to “TodayReference” (see parameters); negative values are for months before TodayReference, positive values are for months after TodayReference
- **Calendar RelativeQuarterPos**: 0 *(Integer)* – Relative quarters compared to “TodayReference” (see parameters); negative values are for quarters before TodayReference, positive values are for quarters after TodayReference. 0 implies same quarter as TodayReference
- **Calendar RelativeYearPos**: 0 *(Integer)* – Relative years compared to “TodayReference” (see parameters); negative values are for years before TodayReference, positive values are for years after TodayReference. 0 implies same year as TodayReference
- **Calendar StartOfMonth**: 3/1/2018 *(Date)* – First day of the month
- **Calendar EndOfMonth**: 3/31/2018 *(Date)* – Last day of the month
- **Calendar StartOfQuarter**: 1/1/2018 *(Date)* – First day of the quarter
- **Calendar EndOfQuarter**: 3/31/2018 *(Date)* – Last day of the quarter
- **Calendar StartOfYear**: 1/1/2018 *(Date)* – First day of the year
- **Calendar EndOfYear**: 12/31/2018 *(Date)* – Last day of the year
- **Calendar MonthDays**: 31 *(Integer)* – Number of days in the month
- **Calendar QuarterDays**: 90 *(Integer)* – Number of days in the quarter
- **Calendar YearDays**: 365 *(Integer)* – Number of days in the year
- **Calendar DayOfMonthNumber**: 21 *(Integer)* – Sequential number of the day within the month (it is like the Day of Month column)
- **Calendar DayOfQuarterNumber**: 79 *(Integer)* – Sequential number of the day within the quarter
- **Calendar DayOfYearNumber**: 79 *(Integer)* – Sequential number of the day within the year
- **Calendar DatePreviousWeek**: 3/14/2018 *(Date)* – Same relative day in previous week
- **Calendar DatePreviousMonth**: 2/21/2018 *(Date)* – Same relative day in previous month
- **Calendar DatePreviousQuarter**: 12/21/2017 *(Date)* – Same relative day in previous quarter
- **Calendar DatePreviousYear**: 3/21/2017 *(Date)* – Same relative day in previous year

## Fiscal Calendar (Fiscal)

*The dates used as examples have been obtained using TodayReference = 1/23/2018 and FiscalCalendarFirstMonth = 9. Thus, Q1 is September-November, Q2 is December-February, and so on.*

- **Fiscal Year**: F 2018 *(String)* – Fiscal Year, sorted by Fiscal YearNumber
- **Fiscal YearNumber**: 2018 *(Integer)* – Fiscal year number
- **Fiscal QuarterNumber**: 3 *(Integer)* – Fiscal quarter number
- **Fiscal Quarter**: FQ3 *(String)* – Fiscal quarter, sorted by Fiscal QuarterNumber
- **Fiscal YearQuarterNumber**: 8074 *(Integer)* – Sequential quarter number across years (= [Fiscal YearNumber] \* 4 + [Fiscal QuarterNumber] – 1)
- **Fiscal Quarter Year**: FQ3 2018 *(String)* – Fiscal quarter and year, sorted by Fiscal YearQuarterNumber
- **Fiscal MonthNumber**: 7 *(Integer)* – Fiscal month number, where 1 is the first month in the fiscal year
- **Fiscal Month**: Mar *(String)* – Fiscal month name, sorted by Fiscal MonthNumber
- **Fiscal YearMonthNumber**: 24222 *(Integer)* – Sequential month number across years (= [Fiscal YearNumber] \* 12 + [Fiscal MonthNumber] – 1)
- **Fiscal Month Year**: Mar 2018 *(String)* – Fiscal month and year, sorted by Fiscal YearMonthNumber
- **Fiscal WeekNumber**: 30 *(Integer)* – Fiscal week number
- **Fiscal Week**: W30 *(String)* – Fiscal week, sorted by Fiscal WeekNumber
- **Fiscal YearWeekNumber**: 6116 *(Integer)* – Sequential year number across years (a week always counts 7 days and a week number can fall between 1 and 52, between December and January) – similar to Calendar YearWeekNumber
- **Fiscal Week Year**: FW30-2018 *(String)* – Fiscal week and year, sorted by Fiscal WeekYearOrder (a fiscal week can count less than 7 days at start and end of year)
- **Fiscal WeekYearOrder**: 201830 *(Integer)* – Fiscal year and week in YYYYWW format (a calendar week can have less than 7 days at start and end of year)
- **Fiscal RelativeWeekPos**: -8 *(Integer)* – Relative weeks compared to “TodayReference” (see parameters); negative values are for weeks before TodayReference, positive values are for weeks after TodayReference
- **Fiscal RelativeMonthPos**: -2 *(Integer)* – Relative months compared to “TodayReference” (see parameters); negative values are for months before TodayReference, positive values are for months after TodayReference
- **Fiscal RelativeQuarterPos**: -1 *(Integer)* – Relative quarters compared to “TodayReference” (see parameters); negative values are for quarters before TodayReference, positive values are for quarters after TodayReference
- **Fiscal RelativeYearPos**: 0 *(Integer)* – Relative years compared to “TodayReference” (see parameters); negative values are for years before TodayReference, positive values are for years after TodayReference
- **Fiscal StartOfMonth**: 3/1/2018 *(Date)* – First day of the fiscal month
- **Fiscal EndOfMonth**: 3/31/2018 *(Date)* – Last day of the fiscal month
- **Fiscal StartOfQuarter**: 3/1/2018 *(Date)* – First day of the fiscal quarter
- **Fiscal EndOfQuarter**: 5/31/2018 *(Date)* – Last day of the fiscal quarter
- **Fiscal StartOfYear**: 9/1/2017 *(Date)* – First day of the fiscal year
- **Fiscal EndOfYear**: 8/31/2018 *(Date)* – Last day of the fiscal year
- **Fiscal MonthDays**: 31 *(Integer)* – Number of days in the fiscal month
- **Fiscal QuarterDays**: 92 *(Integer)* – Number of days in the fiscal quarter
- **Fiscal YearDays**: 365 *(Integer)* – Number of days in the fiscal year
- **Fiscal DayOfMonthNumber**: 21 *(Integer)* – Sequential number of the day within the fiscal month (similar to Day of Month column)
- **Fiscal DayOfQuarterNumber**: 21 *(Integer)* – Sequential number of the day within the quarter
- **Fiscal DayOfYearNumber**: 201 *(Integer)* – Sequential number of the day within the fiscal year
- **Fiscal DatePreviousWeek**: 3/14/2018 *(Date)* – Same relative day in previous fiscal week
- **Fiscal DatePreviousMonth**: 2/21/2018 *(Date)* – Same relative day in previous fiscal month
- **Fiscal DatePreviousQuarter**: 12/21/2017 *(Date)* – Same relative day in previous fiscal quarter
- **Fiscal DatePreviousYear**: 3/21/2017 *(Date)* – Same relative day in previous fiscal year

## Fiscal Weekly Calendar (FW)

*The sample dates have been obtained with TodayReference = 1/23/2018, WeeklyType = Last, QuarterWeekType = 445, and FiscalCalendarFirstMonth = 9.*

- **FW YearNumber**: 2018 *(Integer)* – Fiscal weekly year number
- **FW Year**: FW 2017 *(String)* – Fiscal weekly year, sorted by FW YearNumber
- **FW QuarterNumber**: 3 *(Integer)* – Fiscal weekly quarter number
- **FW Quarter**: FW Q3 *(String)* – Fiscal weekly quarter, sorted by FW QuarterNumber
- **FW YearQuarterNumber**: 8070 *(Integer)* – Sequential quarter number across years (= [FW YearNumber] \* 4 + [FW QuarterNumber] – 1)
- **FW Quarter Year**: FW Q3 2017 *(String)* – Fiscal weekly quarter and year, sorted by FW YearQuarterNumber
- **FW MonthNumber**: 7 *(Integer)* – Fiscal weekly month/period number, where 1 is the first month in the fiscal weekly year
- **FW Month**: FW P07 *(String)* – Fiscal weekly month/period name, sorted by Fiscal MonthNumber
- **FW YearMonthNumber**: 24210 *(Integer)* – Sequential month/period number across years (= [FW YearNumber] \* 12 + [FW MonthNumber] – 1)
- **FW Month Year**: FW P07 2017 *(String)* – Fiscal weekly month/period and year, sorted by FW YearMonthNumber
- **FW PeriodNumber**: 8 *(Integer)* – Fiscal 4-week period number (from 1 to 13, additional 14 period on years with 53 weeks)
- **FW Period**: FW P08 *(String)* – Fiscal 4-week period, sorted by FW PeriodNumber
- **FW WeekNumber**: 30 *(Integer)* – Fiscal weekly week number
- **FW Week**: FW W30 *(String)* – Fiscal weekly week, sorted by FW WeekNumber
- **FW YearWeekNumber**: 6117 *(Integer)* – Sequential year number across years (a week always counts 7 days and a week number can range from 1 to 52, between December and January)
- **FW WeekYear**: FW W30 2017 *(String)* – Fiscal weekly week and year, sorted by FW YearWeekNumber
- **FW StartOfWeek**: 3/18/2018 *(Date)* – First day of the week
- **FW EndOfWeek**: 3/24/2018 *(Date)* – Last day of the week
- **FW RelativeWeekPos**: -8 *(Integer)* – Relative weeks compared to “TodayReference” (see parameters); negative values are for weeks before TodayReference, positive values are for weeks after TodayReference
- **FW RelativeMonthPos**: -1 *(Integer)* – Relative fiscal weekly months/periods compared to “TodayReference” (see parameters); negative values are for months before TodayReference, positive values are for months after TodayReference
- **FW RelativeQuarterPos**: -1 *(Integer)* – Relative fiscal weekly quarters compared to “TodayReference” (see parameters); negative values are for quarters before TodayReference, positive values are for quarters after TodayReference
- **FW RelativeYearPos**: 0 *(Integer)* – Relative fiscal weekly years compared to “TodayReference” (see parameters); negative values are for years before TodayReference, positive values are for years after TodayReference
- **FW StartOfMonth**: 2/25/2018 *(Date)* – First day of the fiscal weekly month/period
- **FW EndOfMonth**: 3/24/2018 *(Date)* – Last day of the fiscal weekly month/period
- **FW StartOfQuarter**: 2/25/2018 *(Date)* – First day of the fiscal weekly quarter
- **FW EndOfQuarter**: 5/26/2018 *(Date)* – Last day of the fiscal weekly quarter
- **FW StartOfYear**: 8/27/2017 *(Date)* – First day of the fiscal weekly year
- **FW EndOfYear**: 8/25/2018 *(Date)* – Last day of the fiscal weekly year
- **FW MonthDays**: 28 *(Integer)* – Number of days in the fiscal weekly month/period
- **FW QuarterDays**: 91 *(Integer)* – Number of days in the fiscal weekly quarter
- **FW YearDays**: 364 *(Integer)* – Number of days in the fiscal weekly year
- **FW DayOfMonthNumber**: 24 *(Integer)* – Sequential number of the day within the month/period of the fiscal weekly calendar
- **FW DayOfQuarterNumber**: 24 *(Integer)* – Sequential number of the day within the quarter of the fiscal weekly calendar
- **FW DayOfYearNumber**: 206 *(Integer)* – Sequential number of the day within the fiscal weekly year
- **FW DatePreviousWeek**: 3/14/2018 *(Date)* – Same relative day in previous week
- **FW DatePreviousMonth**: 2/14/2018 *(Date)* – Same relative day in previous fiscal weekly month/period
- **FW DatePreviousQuarter**: 12/20/2017 *(Date)* – Same relative day in previous fiscal weekly quarter
- **FW DatePreviousYear**: 3/22/2017 *(Date)* – Same relative day in previous fiscal weekly year

## Holidays and Working Days

- **Holiday Name**: (blank) *(String)* – Name of the holiday, (blank) if it is not a holiday
- **IsWorkingDay**: True *(Boolean)* – True for working days, False for non-working days, depending on week day, holidays and configuration in parameters
- **Day Type**: Working day *(String)* – String displaying “Working day” or “Non-working day”, depending on week day, holidays and configuration in parameters

## Parameters

*The initial part of the DAX expression sets several variables. These variables define parameters used to generate the calendars.*

- **TodayReference**
  - *Default: [[TODAY]]()*
  - Defines the reference date (like a “current day”) in the model. If there is data up to 5 days before the reference date, it is possible to change the reference date in the model in order to properly set all the relative calculations of weeks/months/quarters/years.
- **FirstYear**
  - *Default: numeric value, e.g. 2008*
  - First year generated in the Date table. Can be assigned with a dynamic calculation reading the first year of data available in other tables.
- **LastYear**
  - *Default: [[YEAR]] ( TodayReference )*
  - Last year generated in the Date table. If the data model is set up in a way that allows for future dates, it should be set reading the last year of data available in other tables.
- **FiscalCalendarFirstMonth**
  - *Range: a number between 1 and 12*
  - Used for Fiscal monthly (Gregorian) calendar and for Fiscal weekly calendar. If set to 1, the fiscal monthly calendar (Fiscal) is identical to the solar calendar (Calendar). However, the Fiscal weekly calendar (FW) always generates a different result even when this parameter is set to 1.
- **FirstDayOfWeek**
  - *Range: 0 – Sunday, 1 – Monday, 2 – Tuesday, … 5 – Friday, 6 – Saturday*
  - Defines the first day of a week and defines when a week starts in a weekly calendar. US calendars typically use 0 (Sunday), whereas European calendars use 1 (Monday).
- **IsoCountryHolidays**
  - *Range: ISO code of the country (it should be one of the supported countries – US, CA, UK, AU, DE, FR, IT, ES, NL, BE, PT)*
  - Defines which country to use in order to set the holidays in the calendar as non-working days.
- **WeeklyType**
  - *Default: Last*
  - *Range: “Last” or “Nearest”*
  - Determines the end of the year definition for fiscal weekly calendar (FW). Reference for [Last/Nearest definition on Wikipedia](https://en.wikipedia.org/wiki/4%E2%80%934%E2%80%935_calendar).
    - *Last: for last weekday of the month at fiscal year end*
    - *Nearest: for last weekday nearest the end of month*
  - For the ISO calendar use:
    - FiscalCalendarFirstMonth = 1 *(ISO always starts in January)*
    - FirstDayOfWeek = 1 *(ISO always starts on Monday)*
    - WeeklyType = “Nearest” *(ISO uses the nearest week type algorithm)*
  - For US with last Saturday of the month at fiscal year end
    - FirstDayOfWeek = 0 *(US weeks start on Sunday)*
    - WeeklyType = “Last”
  - For US with last Saturday nearest the end of month
    - FirstDayOfWeek = 0 *(US weeks start on Sunday)*
    - WeeklyType = “Nearest”
- **QuarterWeekType**
  - *Default: 445*
  - *Range: “445”, “454”, “544”*
  - Defines the number of weeks per period in each quarter. Quarters which always count 13 weeks in the Fiscal weekly calendar (FW).
- **CalendarRange**
  - *Default: Calendar*
  - *Range: “Calendar”, “FiscalGregorian”, “FiscalWeekly”*
  - Defines to which type of calendar the year boundaries are applied during table’s generation. Using FiscalWeekly the first and last day of the year might not correspond to a first and last day of a month, respectively.
- **CalendarGregorianPrefix**
  - *Default: “” (empty string)*
  - Prefix used in columns of solar Gregorian calendar.
- **FiscalGregorianPrefix**
  - *Default: F*
  - Prefix used in columns of fiscal Gregorian calendar.
- **FiscalWeeklyPrefix**
  - *Default: “FW ”*
  - Prefix used in columns of fiscal weekly calendar.
- **WorkingDayType**
  - *Default: Working day*
  - Description used for working days (could be translated).
- **NonWorkingDayType**
  - *Default: Non-working day*
  - Description used for non-working days (could be translated).
- **WeeklyCalendarType**
  - *Default: Weekly*
  - *Range: “Weekly”, “Custom”*
  - Use Weekly to automatically generate fiscal weekly calendar based on FiscalCalendarFirstMonth, FirstDayOfWeek, and WeeklyType. Use Custom to define arbitrary range of dates for each year defined in CustomFiscalPeriods.
- **WorkingDays**
  - *Default: 1, 2, 3, 4, 5*
  - Defines the working days of a week, Monday to Friday by default (0 = Sunday, 6 = Saturday). The definition should use the [[DATATABLE]] syntax.
- **UseCustomFiscalPeriods**
  - *Default: FALSE*
  - Use CustomFiscalPeriods in case you need arbitrary definition of weekly fiscal years. Set to TRUE in order to use CustomFiscalPeriods variable.
- **IgnoreWeeklyFiscalPeriods**
  - *Default: FALSE*
  - Set IgnoreWeeklyFiscalPeriods to TRUE in order to ignore the WeeklyFiscalPeriods table generated automatically to manage Weekly calendars. You should set IgnoreWeeklyFiscalPeriods to TRUE only when UseCustomFiscalPeriods is TRUE, too.
- **CustomFiscalPeriods**
  - *Default: see sample code*
  - Defines a custom list of fiscal periods that could override the standard generation of weekly calendars. The definition should use the [[DATATABLE]] syntax.

## Download template

Download the latest version of the Reference Date Table template in the [tool page](https://www.sqlbi.com/tools/dax-date-template/).

[[SELECTCOLUMNS]]

Returns a table with selected columns from the table and new columns specified by the DAX expressions.

`SELECTCOLUMNS ( <Table> [[, <Name>], <Expression> [[, <Name>], <Expression> [, … ] ] ] )`

[[TODAY]]

Returns the current date in datetime format.

`TODAY ( )`

[[YEAR]]

Returns the year of a date as a four digit integer.

`YEAR ( <Date> )`

[[DATATABLE]]

Returns a table with data defined inline.

`DATATABLE ( <Name>, <DataType> [, <Name>, <DataType> [, … ] ], <Data> )`
