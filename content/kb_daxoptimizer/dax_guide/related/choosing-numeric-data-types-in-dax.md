---
title: "Choosing Numeric Data Types in DAX"
url: "https://www.sqlbi.com/articles/choosing-numeric-data-types-in-dax"
published: "2020-08-17"
updated: 
source: "sqlbi.com"
---

# Choosing Numeric Data Types in DAX

> 发布：2020-08-17

When you create a data model in Power Pivot, Power BI, or Analysis Services Tabular, every numeric column can have one of the following three data types:

- **Integer** (also known as **Whole number**): Numbers that have no decimal places. Integers can be positive or negative numbers, but must be whole numbers between -9,223,372,036,854,775,808 (-2^63) and 9,223,372,036,854,775,807 (2^63-1). Data stored in 8 bytes.
- **Floating point** (also known as **Decimal number**): Numbers that can have decimal places covering a wide range of values: negative values from -1.79E +308 through -2.23E -308; zero; and positive values from 2.23E -308 through 1.79E + 308. However, the number of significant digits is limited to 15 decimal digits. Data stored in 8 bytes.
- **Fixed decimal number** (also known as **Currency**): Values between -922,337,203,685,477.5808 to 922,337,203,685,477.5807 with four decimal digits of fixed precision. Data stored in 8 bytes.

When you create a data model you have to carefully consider the data type to use for each column. Sometimes the data type chosen by the wizard could produce undesired results. For this reason, it is useful to learn how the default choice is made when you import a table. The article will reference to Tabular models as a generic way to indicate a data model created in Power Pivot, Power BI, or Analysis Services Tabular.

You should not confuse the data type with the format string. Especially in Power Pivot, this was very confusing, because the same name (Currency) is used for both the data type and the format string. Power BI now uses “Fixed Decimal Number” to identify the data type, and “Currency” to identify the format string, so this should help avoiding confusion between the two. If you are using Power Pivot, please be aware of the correspondence between Fixed Decimal Number and Currency in terms of data type.

## Mapping between SQL Server and Tabular data types

When you read a table from SQL Server, by default you obtain the following Tabular data types:

- **Integer** (also known as **Whole number**). Created for following data types in T-SQL: BIGINT, [[INT]], SMALLINT, and TINYINT.
- **Floating point** (also known as **Decimal number**): Created for following data types in T-SQL: NUMERIC, DECIMAL, FLOAT, REAL. The floating point is the default choice for NUMERIC and DECIMAL, regardless of the number of digits declared for a table column.
- **Fixed decimal number** (also known as **Currency**): Created for following data types in T-SQL: MONEY and SMALLMONEY.

Please note that the fixed decimal number is used only for MONEY ad SMALLMONEY data type. Even if a data type NUMERIC ( 9, 2 ) would fit in a Fixed Decimal Number, the corresponding data type in Tabular is always a floating point by default. As you will see in the next sections, it is always recommended to change a data type to Fixed Decimal Number in case it contains a financial value.

## Integers and Fixed Decimal Number

Integer values are stored with no approximation, math on integers is fully associative and, moreover, it is the fastest way of computing values. Fixed Decimal Number is internally stored as Integer, because the limit of four digits of decimal points let the system perform all the math using Integers and then divide by 10,000 only at the end. Integers and Fixed Decimal Numbers are your best option to store numbers related to financial transactions, for both performance and accuracy in calculation.

The result of an aggregation of Integers or Fixed Decimal Number should be a number of the same type. This could be a constraint for your data model, because if you have very large numbers, their sum could exceed the limit that could be represented in such a data type. If you are aware of this issue, you should consider using a different data type to store your numbers, because overflow calculation errors are not detected by the engine.

## The risk of using a floating point aggregating data

It is a well know issue the fact that floating point values store an approximation of the original value. However, it is less known that the aggregations of Tabular data stored in a floating point column could provide a different result for different executions of the same query.

A Tabular model stores data in segments, which default size is one million rows in Power Pivot and eight million rows in SSAS Tabular. Each segment might be scanned by a different thread, so having more cores available allows an increased parallelization of the query execution. For a floating point column, each core computes an intermediate value of the complete aggregation, and the values collected from each segment are then aggregated using floating point arithmetic again. When parallel execution is involved, it becomes hard to reproduce the exact sequence and duration of events, and this can generate different results by aggregating different segments in different calculations. The reason is that different segments are scanned by multiple cores in an unpredictable fashion, so the order in which numbers get aggregated may vary, which leads to different results because floating point operations are not associative.

In practice, even a simple [[SUM]] ( table[column] ) can be affected by the behavior described, generating different results for measures that aggregates a floating point column using at least 2 cores from the same machine. The higher the number of columns and the size of the data, the more likely this behavior will happen. Usually the calculation error is really tiny, but it could be visible to the end users, who might lose trustworthy in the system.

## Final Considerations

Choosing the right data type for a Tabular model is of paramount importance to avoid rounding errors of floating point values that, in certain calculations, could propagate to a more visible level of precision of the numbers you compute.

Rounding errors can generate different results in different execution of the same query over the same data model, because of the different order and timing of segment scans made by the storage engine on large tables, which enables parallelization of the execution.

The biggest challenge is that most of the columns imported from a SQL Server database are usually defined as NUMERIC or DECIMAL, with a number of decimal points and a requirement for precision that would fit in a Fixed Decimal Number data type in Tabular. However, by default such a columns are imported as floating point, so it is probably necessary to review data types after you add a table in a Tabular model.

[[INT]]

Rounds a number down to the nearest integer.

`INT ( <Number> )`

[[SUM]]

Adds all the numbers in a column.

`SUM ( <ColumnName> )`
