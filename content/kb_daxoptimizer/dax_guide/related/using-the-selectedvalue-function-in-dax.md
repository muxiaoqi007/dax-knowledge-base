---
title: "Using the SELECTEDVALUE function in DAX"
url: "https://www.sqlbi.com/articles/using-the-selectedvalue-function-in-dax"
published: "2024-03-16"
updated: 
source: "sqlbi.com"
---

# Using the SELECTEDVALUE function in DAX

> 发布：2024-03-16

**UPDATE 2020-01-28**: The original article has been updated also adding performance considerations.  
**UPDATE 2022-06-11**: Added considerations to use alternatives to [[SELECTEDVALUE]] when using [Fields Parameters in Power BI](https://www.sqlbi.com/articles/fields-parameters-in-power-bi/).

The DAX language grows over time thanks to the monthly updates of Power BI, which gradually introduce new features later made available also in Analysis Services and Power Pivot. The [[SELECTEDVALUE]] function has been introduced in July 2017 and it is available in Power BI Desktop and Analysis Services, but not in Power Pivot for Excel. The [[SELECTEDVALUE]] function did not introduce a new feature – it just simplifies the syntax required in common coding patterns, as you will see in a few of the possible uses presented after the description of the [[SELECTEDVALUE]] syntax.

## [[SELECTEDVALUE]] syntax

The function [[SELECTEDVALUE]] returns the value of the column reference passed as first argument if it is the only value available in the filter context, otherwise it returns blank or the default value passed as the second argument. Here are a few examples of possible syntax.

```dax


SELECTEDVALUE ( Table[column] )

SELECTEDVALUE ( Table[column], "default value" )

SELECTEDVALUE ( Table[column], 0 )

```

Internally, [[SELECTEDVALUE]] is just syntax sugar generating the following corresponding syntaxes:

```dax


IF ( HASONEVALUE ( Table[column] ), VALUES ( Table[column] ) )

IF ( HASONEVALUE ( Table[column] ), VALUES ( Table[column] ), "default value" )

IF ( HASONEVALUE ( Table[column] ), VALUES ( Table[column] ), 0 )

```

You should use [[SELECTEDVALUE]] in all those cases when you need to read a single value selected in the filter context, obtaining blank or another default value in all other cases.

It is important to remember that:

- The [[SELECTEDVALUE]] and [[VALUES]] functions read the current filter context, not the row context;
- When you have a row context, just use a column reference (within [[RELATED]] in case it is in a lookup table);
- If the filter context returns zero rows for the referenced column, then [[SELECTEDVALUE]] returns the second argument – do not assume that the second argument is returned only when two or more values are selected;
- If you use a version of DAX that does not have [[SELECTEDVALUE]], you can use the same pattern as that described in this article, replacing [[SELECTEDVALUE]] with the corresponding syntax using [[HASONEVALUE]] / [[VALUES]].

## Sample uses of [[SELECTEDVALUE]]

This section describes several use cases for [[SELECTEDVALUE]]. The Power BI file you can download contains all the examples described in this section.

### Retrieving a column from the same table

You can retrieve the attribute of an entity (e.g. the current class of a product) displayed in a matrix, making sure you are not going to duplicate the rows in case there are more attribute values available for that same selection.

```dax


Product Class := SELECTEDVALUE ( 'Product'[Class] )

```

[![](https://cdn.sqlbi.com/wp-content/uploads/SelectedValue-01.png)](https://cdn.sqlbi.com/wp-content/uploads/SelectedValue-01.png)

This measure is useful when you navigate using a Matrix or a Pivot Table. However, the measure displays data even for products that have no sales. This did not happen when you used a Table visual in older versions of Power BI; **as of March 2024, the behavior is the same as described before. We do not know exactly when this changed after we published the article**.

[![](https://cdn.sqlbi.com/wp-content/uploads/SelectedValue-02.png)](https://cdn.sqlbi.com/wp-content/uploads/SelectedValue-02.png)

If you want to show the class of a product only when there is another measure returning data, you should add a condition as in one of the two following examples:

```dax


Product Class v1 := 

IF ( 

    NOT ISBLANK ( [Sales Amount] ), 

    SELECTEDVALUE ( 'Product'[Class] ) 

)



Product Class v2 := 

IF ( 

    NOT ISEMPTY ( Sales ), 

    SELECTEDVALUE ( 'Product'[Class] ) 

)

```

[![](https://cdn.sqlbi.com/wp-content/uploads/SelectedValue-03.png)](https://cdn.sqlbi.com/wp-content/uploads/SelectedValue-03.png)

### Retrieving a column from a lookup table

If you want to retrieve the corresponding attribute in a lookup table, you probably need to modify the propagation of the filter context through relationships. When you have a row context you would use a single [[RELATED]] function to retrieve the corresponding row context in a lookup table, regardless of the number of relationships to travel. For example, consider the case of a snowflake dimension featuring the *Product*, *Product Subcategory*, and *Product Category* tables. If you want to get the current category of a product in a Matrix or a PivotTable, you need the following code:

```dax


Product Category := 

CALCULATE ( 

    SELECTEDVALUE ( 'Product Category'[Category] ), 

    CROSSFILTER (

        'Product'[ProductSubcategoryKey], 

        'Product Subcategory'[ProductSubcategoryKey], 

        Both 

    ),

    CROSSFILTER ( 

        'Product Subcategory'[ProductCategoryKey], 

        'Product Category'[ProductCategoryKey], 

        Both 

    )

)

```

In this case, a simpler syntax could be the one provided by the expanded table:

```dax


Product Category exp.table := 

CALCULATE ( 

    SELECTEDVALUE ( 'Product Category'[Category] ), 

    'Product'

)

```

However, remember that the expanded table might have undesired side effects in complex data models, so the [[CROSSFILTER]] solution should be preferred. Both measures return the same result.

[![](https://cdn.sqlbi.com/wp-content/uploads/SelectedValue-04.png)](https://cdn.sqlbi.com/wp-content/uploads/SelectedValue-04.png)

### Using a numeric column in a calculation

The [[SELECTEDVALUE]] function simplifies the syntax required when you use a numeric column of an entity as a parameter in a calculation. For example, the following measure calculates the quantity by dividing the existing *Sales Amount* measure by the *Unit Price* value of the selected product. In case more products are selected, the blank result of [[SELECTEDVALUE]] automatically propagates into the result of *Calc Quantity*.

```dax


Calc Quantity := 

DIVIDE ( 

    [Sales Amount], 

    SELECTEDVALUE ( 'Product'[Unit Price] ) 

)

```

[![](https://cdn.sqlbi.com/wp-content/uploads/SelectedValue-05.png)](https://cdn.sqlbi.com/wp-content/uploads/SelectedValue-05.png)

### Parameter table pattern

If you consider the [parameter table pattern](http://www.daxpatterns.com/parameter-table/), you can implement a measure using a parameter by using [[SELECTEDVALUE]] writing this:

```dax


Sales by Scale := 

DIVIDE ( 

    [Sales Amount],

    SELECTEDVALUE ( 'Scale'[Scale], 1 )

)

```

Instead of that:

```dax


Sales by Scale := 

DIVIDE ( 

    [Sales Amount],

    IF ( HASONEVALUE ( Scale[Scale] ), VALUES ( Scale[Scale] ), 1 )

)

```

The result is the same, you just simplify your code.

[![](https://cdn.sqlbi.com/wp-content/uploads/SelectedValue-06.png)](https://cdn.sqlbi.com/wp-content/uploads/SelectedValue-06.png)

If you want to avoid a multiple selection, you could use the [[ERROR]] function, so the visual displays a proper error message (even if the reference to MdxScript seems like nonsense in a DAX client such as Power BI Desktop).

```dax


Sales by Scale Checked := 

DIVIDE ( 

    [Sales Amount],

    SELECTEDVALUE ( 'Scale'[Scale], ERROR ( "Single selection required" ) )

)

```

[![](https://cdn.sqlbi.com/wp-content/uploads/SelectedValue-07.png)](https://cdn.sqlbi.com/wp-content/uploads/SelectedValue-07.png)

## [[SELECTEDVALUE]] with Fields Parameters in Power BI

The [Fields Parameters feature in Power BI](https://www.sqlbi.com/articles/fields-parameters-in-power-bi/) generates a slicer that cannot be read by using [[SELECTEDVALUE]] because internally the table uses the “Group By Columns” feature of the Tabular model (more details in the Grouping Columns section of the Tabular Presentation Layer in [Mastering Tabular video course](https://www.sqlbi.com/p/mastering-tabular-video-course/)).  
[[SELECTEDVALUE]] does not support retrieving data from a column that is grouped by other columns because all the columns involved must be included in the same DAX grouping operation (such as [[SUMMARIZE]] or [[SUMMARIZECOLUMNS]]).  
The code required to replace [[SELECTEDVALUE]] for the column visible in the table generated by Fields Parameter is the following:

```dax


VAR __SelectedValue = 

    SELECTCOLUMNS (

        SUMMARIZE ( Parameter, Parameter[Parameter], Parameter[Parameter Fields] ),

        Parameter[Parameter]

    )

RETURN IF ( COUNTROWS ( __SelectedValue ), __SelectedValue )

```

## Performance considerations

The [[SELECTEDVALUE]] function does not produce a different query plan compared to the extended syntax using [[HASONEVALUE]] and [[VALUES]]. Both [[HASONEVALUE]] and [[SELECTEDVALUE]] internally introduce a behavior similar to the request of [[DISTINCT]] or [[DISTINCTCOUNT]]: all these functions might inflate the number of storage engine requests when they are invoked for multiple cells computed in a filter context propagated through limited relationships rather than regular relationships. For this reason, it is important to use [[SELECTEDVALUE]] or an equivalent syntax only in the high-level calculation, and not in measures that are evaluated often inside large iterations.

## Conclusion

The [[SELECTEDVALUE]] function simplifies the syntax of a common pattern involving two functions ([[HASONEVALUE]] and [[VALUES]]) to retrieve a value from the filter context. Just remember that this technique is not required when you have a row context, because you can simply use a column reference and the [[RELATED]] function when a lookup table is involved.

[[SELECTEDVALUE]]

Returns the value when there’s only one value in the specified column, otherwise returns the alternate result.

`SELECTEDVALUE ( <ColumnName> [, <AlternateResult>] )`

[[VALUES]]

When a column name is given, returns a single-column table of unique values. When a table name is given, returns a table with the same columns and all the rows of the table (including duplicates) with the [additional blank row](https://www.sqlbi.com/articles/blank-row-in-dax/) caused by an invalid relationship if present.

`VALUES ( <TableNameOrColumnName> )`

[[RELATED]]

Returns a related value from another table.

`RELATED ( <ColumnName> )`

[[HASONEVALUE]]

Returns true when there’s only one value in the specified column.

`HASONEVALUE ( <ColumnName> )`

[[CROSSFILTER]]

CALCULATE modifier

Specifies cross filtering direction to be used in the evaluation of a DAX expression. The relationship is defined by naming, as arguments, the two columns that serve as endpoints.

`CROSSFILTER ( <LeftColumnName>, <RightColumnName>, <CrossFilterType> )`

[[ERROR]]

Raises a user specified error.

`ERROR ( <ErrorText> )`

[[SUMMARIZE]]

Creates a summary of the input table grouped by the specified columns.

`SUMMARIZE ( <Table> [, <GroupBy_ColumnName> [, [<Name>] [, [<Expression>] [, <GroupBy_ColumnName> [, [<Name>] [, [<Expression>] [, … ] ] ] ] ] ] ] )`

[[SUMMARIZECOLUMNS]]

Create a summary table for the requested totals over set of groups.

`SUMMARIZECOLUMNS ( [<GroupBy_ColumnName> [, [<FilterTable>] [, [<Name>] [, [<Expression>] [, <GroupBy_ColumnName> [, [<FilterTable>] [, [<Name>] [, [<Expression>] [, … ] ] ] ] ] ] ] ] ] )`

[[DISTINCT]]

Returns a one column table that contains the distinct (unique) values in a column, for a column argument. Or multiple columns with distinct (unique) combination of values, for a table expression argument.

`DISTINCT ( <ColumnNameOrTableExpr> )`

[[DISTINCTCOUNT]]

Counts the number of distinct values in a column.

`DISTINCTCOUNT ( <ColumnName> )`
