---
title: "From SQL to DAX: String Comparison"
url: "https://www.sqlbi.com/articles/from-sql-to-dax-string-comparison"
published: "2020-08-17"
updated: 
source: "sqlbi.com"
---

# From SQL to DAX: String Comparison

> 发布：2020-08-17

**[UPDATE 2020-07-12] Article updated adding [[CONTAINSSTRING]] and [[CONTAINSSTRINGEXACT]].**

If you compare two strings by using common operators (=, <>, <, >, <=, >=) the string comparison can be case-insensitive, according to the collation setting of your Analysis Services instance (for PowerPivot it depends on the workbook). However, functions like [[FIND]] and [[SUBSTITUTE]] are always case-sensitive, whereas [[SEARCH]] is always case-insensitive. [[FIND]] can be 10% faster than [[SEARCH]] just because it is case-sensitive. More recent versions of DAX introduced a simplified syntax with [[CONTAINSSTRING]] and [[CONTAINSSTRINGEXACT]]. [[CONTAINSSTRING]] provides the same features of [[SEARCH]], whereas [[CONTAINSSTRINGEXACT]] provides the same features of [[FIND]].

One issue you might have if you come from SQL is the different operator you have to perform somewhat similar to a regular expression. In fact, the LIKE operators in SQL has a similar correspondent function in DAX: the [SEARCH](https://www.sqlbi.com/broken/?url=http://technet.microsoft.com/en-us/library/ee634235(SQL.110).aspx) function has a slightly different semantic because it returns the position found instead of a True/False value. Moreover, it has different wildcard characters, as you will see later in this article.

For example, consider the following syntax in SQL:

```dax
Name LIKE '%SQLBI%'
```

In Power BI, Analysis Services 2019, and Azure Analysis Services you can write the following equivalent syntax:

```dax
CONTAINSSTRING( Table[Name], "SQLBI" )
```

In Tabular and PowerPivot v2 (for SQL Server 2012) you can write the following equivalent syntax:

```dax
SEARCH( "SQLBI", Table[Name], 1, 0 ) > 0
```

The condition above returns 0 if SQLBI is not found in the Name column of Table. The search starts at first character (the third parameter) and in case of no match you get 0 as a result (defined by the fourth parameter). If you are using PowerPivot for SQL Server 2008 R2 (or PowerPivot v1), you have to wrap [[SEARCH]] into an [[IFERROR]] because the fourth parameter has been introduced only in SQL Server 2012 release and an error is thrown in PowerPivot version 1.

```dax
IFERROR( SEARCH( "*SQLBI*", Table[Name], 1 ), 0 ) > 0
```

Unfortunately, the presence of [[IFERROR]] has a big impact on performance and you can observe 4x slower execution times with this syntax. Thus, avoid [[IFERROR]] if possible. If you can use [[CONTAINSSTRING]], you certainly do not have to use [[IFERROR]].

In case you need to define a more complex filter, you have to adapt to the different wildcard characters. Instead of using % and \_ you would use in the SQL LIKE operator, you have to use \* and ? in the DAX [[SEARCH]] function.

For example, consider the following condition in SQL:

```dax
Name LIKE '%SQLBI%Methodology%at%work%'
```

The correspondent syntaxes in DAX are:

```dax


CONTAINSSTRING( Table[Name], "SQLBI*Methodology*at*work" )

SEARCH( "SQLBI*Methodology*at*work", Table[Name], 1, 0 ) > 0

```

However, if you change the SQL condition to:

```dax
Name LIKE '%SQLBI%Methodology%at%work'
```

you do not have an equivalent syntax in DAX, because you cannot check that the string ends with “work” based only on the return value of the [[SEARCH]] call. Moreover, if you want to improve performance, you should avoid [[SEARCH]] whenever possible. For example, using [[LEFT]] instead of [[SEARCH]] in order to check whether a string begins with a particular text might improve performance of a 5x-10x factor. Here are a few hints in order to translate LIKE in the best pattern.

|  |  |
| --- | --- |
| **SQL** | **DAX** |
| ```dax Name LIKE 'SQLBI' ``` | ```dax Table[Name] = "SQLBI" ``` |
| ```dax Name LIKE 'SQLBI%' ``` | ```dax LEFT( Table[Name], 5 ) = "SQLBI" ``` |
| ```dax Name LIKE '%SQLBI' ``` | ```dax RIGHT( Table[Name], 5 ) = "SQLBI" ``` |
| ```dax Name LIKE '%SQLBI%' ``` | ```dax CONTAINSSTRING( Table[Name], "SQLBI" ) ```   for PowerPivot v2 or later and Analysis Services 2012/2014/2016/2017:   ```dax SEARCH( "SQLBI", Table[Name], 1, 0 ) > 0 ```   for PowerPivot v1:   ```dax IFERROR( SEARCH( "SQLBI", Table[Name], 1 ), 0 ) > 0 ``` |
| ```dax Name LIKE 'SQLBI%Methodology' ``` | ```dax LEFT( Table[Name], 5 ) = "SQLBI"
 && RIGHT( Table[Name], 11 ) = "Methodology" ``` |
| ```dax Name LIKE 'SQLBI%Methodology%' ``` | ```dax LEFT( Table[Name], 5 ) = "SQLBI"
 && CONTAINSSTRING( Table[Name], "Methodology" ) ```   for PowerPivot v2 or later and Analysis Services 2012/2014/2016/2017:   ```dax LEFT( Table[Name], 5 ) = "SQLBI"
 && SEARCH( "Methodology", Table[Name], 1, 0 ) > 0 ``` |
| ```dax Name LIKE '%SQLBI%Methodology%' ``` | ```dax CONTAINSSTRING( Table[Name], "SQLBI*Methodology" ) ```   for PowerPivot v2 or later and Analysis Services 2012/2014/2016/2017:   ```dax SEARCH( "SQLBI*Methodology", Table[Name], 1, 0 ) > 0 ``` |

In conclusion, in DAX it is better to avoid the use of [[CONTAINSSTRING]] or [[SEARCH]] unless you really need to search a pattern using wildcards, and it is highly suggested to avoid using [[IFERROR]] for performance reasons (you pay the penalty for every error raised – using [[IFERROR]] when the errors are really rare is fine).

[[CONTAINSSTRING]]

Returns TRUE if one text string contains another text string. CONTAINSSTRING is not case-sensitive, but it is accent-sensitive.

`CONTAINSSTRING ( <WithinText>, <FindText> )`

[[CONTAINSSTRINGEXACT]]

Returns TRUE if one text string contains another text string. CONTAINSSTRINGEXACT is case-sensitive and accent-sensitive.

`CONTAINSSTRINGEXACT ( <WithinText>, <FindText> )`

[[FIND]]

Returns the starting position of one text string within another text string. FIND is case-sensitive and accent-sensitive.

`FIND ( <FindText>, <WithinText> [, <StartPosition>] [, <NotFoundValue>] )`

[[SUBSTITUTE]]

Replaces existing text with new text in a text string.

`SUBSTITUTE ( <Text>, <OldText>, <NewText> [, <InstanceNumber>] )`

[[SEARCH]]

Returns the starting position of one text string within another text string. SEARCH is not case-sensitive, but it is accent-sensitive.

`SEARCH ( <FindText>, <WithinText> [, <StartPosition>] [, <NotFoundValue>] )`

[[IFERROR]]

Returns value\_if\_error if the first expression is an error and the value of the expression itself otherwise.

`IFERROR ( <Value>, <ValueIfError> )`

[[LEFT]]

Returns the specified number of characters from the start of a text string.

`LEFT ( <Text> [, <NumberOfCharacters>] )`

## Articles in the From SQL to DAX series

- *From SQL to DAX: String Comparison*
- [From SQL to DAX: Filtering Data](https://www.sqlbi.com/articles/from-sql-to-dax-filtering-data/)
- [From SQL to DAX: Grouping Data](https://www.sqlbi.com/articles/from-sql-to-dax-grouping-data/)
- [From SQL to DAX: IN and EXISTS](https://www.sqlbi.com/articles/from-sql-to-dax-in-and-exists/)
- [From SQL to DAX: Joining Tables](https://www.sqlbi.com/articles/from-sql-to-dax-joining-tables/)
- [From SQL to DAX: Implementing NULLIF and COALESCE in DAX](https://www.sqlbi.com/articles/from-sql-to-dax-implementing-nullif-and-coalesce-in-dax/)
- [From SQL to DAX: Projection](https://www.sqlbi.com/articles/from-sql-to-dax-projection/)
