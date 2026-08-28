---
title: "Handling BLANK in DAX"
url: "https://www.sqlbi.com/articles/blank-handling-in-dax"
published: "2023-07-31"
updated: 
source: "sqlbi.com"
---

# Handling BLANK in DAX

> 发布：2023-07-31

**UPDATE 2023-03-24** : Added examples of expressions containing a [[BLANK]] value.  
**UPDATE 2020-06-12** : I added the == operator in the table  
**UPDATE 2016-09-14** : I updated this article adding the last section “Handling [[BLANK]] in Boolean expressions”, reflecting a change recently introduced in the DAX language.

## What is a blank value in DAX

Any column data type in DAX can have a blank value. This is the value assigned to a column when the data source contains a NULL value. If you are used to SQL, you might expect a propagation of the blank value in a DAX expression, but in reality the behavior is different. When you evaluate a DAX expression, a blank value is always converted to 0 or to an empty string, depending on the data type requested by the expression, unless it is evaluated in any term of a multiplication, in which case the blank value propagates in the multiplication result.

You can obtain a blank value in DAX calling the [[BLANK]] function. The following table shows what is the result of several expression containing a blank value. You can replace the call to the [[BLANK]] function with any DAX expression returning a blank value, including a column reference.

| Expression | Result |
| --- | --- |
| ```dax BLANK () ``` | *Blank value* |
| ```dax BLANK () = 0 ``` | True |
| ```dax BLANK () = FALSE ``` | True |
| ```dax BLANK () == FALSE ``` | False |
| ```dax BLANK () == 0 ``` | False |
| ```dax BLANK () == BLANK() ``` | True |
| ```dax BLANK () && TRUE ``` | False |
| ```dax BLANK () || TRUE ``` | True |
| ```dax BLANK () + 4 ``` | 4 |
| ```dax BLANK () + BLANK() ``` | *Blank value* |
| ```dax BLANK () - 4 ``` | -4 |
| ```dax BLANK () - BLANK() ``` | *Blank value* |
| ```dax 4 / BLANK () ``` | *Infinite* |
| ```dax BLANK () / BLANK () ``` | *Blank value* |
| ```dax BLANK () * 4 ``` | *Blank value* |
| ```dax BLANK () / 4 ``` | *Blank value* |
| ```dax BLANK () ^ 1 ``` | *Blank value* |
| ```dax 1 ^ BLANK () ``` | 1 |
| ```dax INT ( BLANK () ) ``` | *Blank value* |
| ```dax CONVERT ( BLANK (), INTEGER ) ``` | *Blank value* |
| ```dax BLANK () = "" ``` | True |
| ```dax BLANK () == "" ``` | False |
| ```dax BLANK () & "A" ``` | A |
| ```dax "A" & BLANK () ``` | A |
| ```dax BLANK () IN { 0 } ``` | False |
| ```dax BLANK () IN { BLANK() } ``` | True |

## Comparisons with [[BLANK]]

When you use [[BLANK]] in a comparison, the conversion to 0 or to an empty string results in a positive match with these values. For this reason, comparing an expression to blank requires a specific function, [[ISBLANK]], which returns true whenever its only argument is a blank expression. The next table shows a number of examples that illustrates how you obtain a positive match comparing a blank values with an empty string and a zero value.

| Expression | Result |
| --- | --- |
| ```dax IF ( BLANK (), "true", "false" ) ``` | false |
| ```dax IF ( ISBLANK ( BLANK () ), "true", "false" ) ``` | true |
| ```dax IF ( BLANK () = True, "true", "false" ) ``` | false |
| ```dax IF ( BLANK () = False, "true", "false" ) ``` | true |
| ```dax IF ( BLANK () = 0, "true", "false" ) ``` | true |
| ```dax IF ( BLANK () = "", "true", "false" ) ``` | true |

The automatic conversion to zero or empty string of a blank value might have undesired side effects. For example, consider the following expression and the result for different values of [measure].

```dax


IF ( 

    [measure] = BLANK(), 

    "is blank", 

    IF ( 

        VALUE ( [measure] ) = 0, 

        "is zero", "other" 

    ) 

)

```

| [measure] | Result |
| --- | --- |
| 0 | is blank |
| “” | is blank |
| [[BLANK]] | is blank |

All the three values (zero, empty string, and blank) match the first condition of the two [[IF]] statements, and the second [[IF]] is never executed.

If you invert the two comparisons, now there are two values (zero and blank) that match the first condition of the two [[IF]] statements, and the second is never executed.

```dax


IF ( 

    VALUE ( [measure] ) = 0, 

    "is blank", 

    IF ( 

        [measure] = BLANK(), 

        "is zero", "other"

    ) 

)

```

| [measure] | Result |
| --- | --- |
| 0 | is blank |
| “” | error |
| [[BLANK]] | is blank |

In this case, the measures containing an empty string generates an error during evaluation, because it cannot be converted to a valid value by the [[VALUE]] function. When you use nested [[IF]] functions, the order of comparisons to equivalent [[BLANK]] values is important, because only the first one matches the comparison.

When you use [[SWITCH]], the behavior is different, because it applies a different comparison logic that matches the [[BLANK]] value using the [[ISBLANK]] function internally. As you can see in the following table, the result of a comparison to an expression that evaluates to [[BLANK]] matches the first blank or false value found in the list.

| Expression | Result |
| --- | --- |
| ```dax SWITCH (
     BLANK(),
     0, "zero",
     BLANK(), "blank",
     True, "true",
     False, "false"
 ) ``` | blank |
| ```dax SWITCH (
     BLANK(), 
     0, "zero", 
     True, "true",
     BLANK(), "blank", 
     False, "false" 
 ) ``` | blank |
| ```dax SWITCH (
     BLANK(), 
     0, "zero", 
     True, "true", 
     False, "false",
     BLANK(), "blank" 
 ) ``` | false |

It is interesting that the comparison with False might get precedence. In order to avoid any issue, the comparison with [[BLANK]] should be the first one in the switch list.

## Automatic conversion from table expressions

A table expression converted to a scalar value generates a [[BLANK]] value when the table is empty. This is particularly important for functions such as [[COUNTROWS]]. For example, the following [[FILTER]] always return an empty table, so the result provided by [[COUNTROWS]] in the following example is blank instead of zero. You have to add a zero value in case you want to make sure the result is a number that you can use in a [[SWITCH]] function, as you see in the following example.

| Expression | Result |
| --- | --- |
| ```dax COUNTROWS ( FILTER ( Table, False ) ) ``` | *Blank value* |
| ```dax COUNTROWS ( FILTER ( Table, False ) ) + 0 ``` | *0* |

The previous example is important to understand why a [[COUNTROWS]] used in a [[SWITCH]] statement might result in unexpected behavior. You can see that in the following table the first example shows a common error, assuming that the expression returns 0 whereas it returns blank, which does not match any value and returns the “other” string. The second example matches the blank value, and the third example matches the zero value by summing zero to the result of [[COUNTROWS]].

| Expression | Result |
| --- | --- |
| ```dax SWITCH ( 
     COUNTROWS ( FILTER ( Query1, False ) ), 
     0, "zero", 
     1, "one", 
     "other" 
 )
 ``` | other |
| ```dax SWITCH ( 
     COUNTROWS ( FILTER ( Query1, False ) ), 
     BLANK(), "blank",
     0, "zero", 
     1, "one", 
     "other" 
 )
 ``` | blank |
| ```dax SWITCH ( 
     COUNTROWS ( FILTER ( Query1, False ) ) + 0, 
     BLANK(), "blank",
     0, "zero", 
     1, "one", 
     "other" 
 )
 ``` | zero |

## Handling [[BLANK]] in Boolean expressions

There is a particular behavior when a column defined as Boolean data type is involved in an expression. Former versions of DAX allowed [[BLANK]] results from a Boolean expression, whereas the more recent versions (Power BI/Excel 2016/SSAS Tabular 2016) only return TRUE or FALSE from a logical expression such as [[IF]]. If you copy a Boolean column in an expression, the existing [[BLANK]] in the table are preserved. Otherwise, a [[BLANK]] in a logical expression is converted to a FALSE value.

As a result of this logic, you can see in the following examples that a [[BLANK]] value is never returned by [[IF]] in case the result should be a Boolean data type.

| Expression | Result |
| --- | --- |
| ```dax IF ( 1 = 1, BLANK(), TRUE ) ``` | *FALSE* |
| ```dax IF ( 1 <> 1, BLANK(), TRUE ) ``` | *TRUE* |
| ```dax IF ( 1 = 1, TRUE, BLANK() ) ``` | *TRUE* |
| ```dax IF ( 1 <> 1, TRUE, BLANK() ) ``` | *FALSE* |

I received the explanation of this change from [Jeffrey Wang (Microsoft)](https://pbidax.wordpress.com/):

> The behavior change only kicks in when [[BLANK]] is casted to a Boolean value in DAX. When the original column is already of Boolean data type, there is no cast, hence [[BLANK]] is preserved.
>
> [[SWITCH]] can be rewritten as nested [[IF]], so we only need to consider [[IF]] function.
>
> When the two branches of [[IF]] have two different data types, [[IF]] returns variant data type. Since calculated column is always strongly typed, it raises error if the underlying DAX expression is of variant data type.
>
> [[BLANK]] value by itself can be treated as of any (undefined) data type, so **[[IF]] (<condition>, <expression>, [[BLANK]]) and [[IF]] (<condition>, [[BLANK]], <expression>)** are treated as the data type of <expression>. When <expression> is of Boolean data type, [[IF]] is of Boolean data type and an implicit cast is applied to [[BLANK]] which converts to FALSE.
>
> But we don’t allow measures to be of undefined data type, so when measure is defined as **[[BLANK]]()**, we assign it the data type Whole Number, as a result, [[IF]] (<condition>, <boolean expression>, [BlankMeasure]) is of variant data type which would result in an error if used to define a calculated column.
>
> When the measure is defined as [[SELECTCOLUMNS]](filter(‘Table1’,’Table1′[Column1] = “Row3″),”Column2”,[Column2]), it picks up the data type of [Column2] which is Boolean. As a result, [[IF]](<condition>, <Boolean expression>, [BlankMeasure]) is of Boolean data type which is ok to define a calculated column.

Understanding how DAX handles blank value is important when you compare values of expressions, in order to avoid possible coding errors caused by wrong initial assumptions.

[[BLANK]]

Returns a blank.

`BLANK ( )`

[[ISBLANK]]

Checks whether a value is blank, and returns TRUE or FALSE.

`ISBLANK ( <Value> )`

[[IF]]

Checks whether a condition is met, and returns one value if TRUE, and another value if FALSE.

`IF ( <LogicalTest>, <ResultIfTrue> [, <ResultIfFalse>] )`

[[VALUE]]

Converts a text string that represents a number to a number.

`VALUE ( <Text> )`

[[SWITCH]]

Returns different results depending on the value of an expression.

`SWITCH ( <Expression>, <Value>, <Result> [, <Value>, <Result> [, … ] ] [, <Else>] )`

[[COUNTROWS]]

Counts the number of rows in a table.

`COUNTROWS ( [<Table>] )`

[[FILTER]]

Returns a table that has been filtered.

`FILTER ( <Table>, <FilterExpression> )`

[[SELECTCOLUMNS]]

Returns a table with selected columns from the table and new columns specified by the DAX expressions.

`SELECTCOLUMNS ( <Table> [[, <Name>], <Expression> [[, <Name>], <Expression> [, … ] ] ] )`
