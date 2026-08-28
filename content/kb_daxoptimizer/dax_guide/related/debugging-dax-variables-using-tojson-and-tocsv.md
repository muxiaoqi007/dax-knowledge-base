---
title: "Debugging DAX variables using TOJSON and TOCSV"
url: "https://www.sqlbi.com/articles/debugging-dax-variables-using-tojson-and-tocsv"
published: "2026-02-25"
updated: 
source: "sqlbi.com"
---

# Debugging DAX variables using TOJSON and TOCSV

> 发布：2026-02-25

In a previous article, [Debugging DAX measures in Power BI](https://www.sqlbi.com/articles/debugging-dax-measures-in-power-bi/), we described several techniques to find errors in a DAX formula. The most basic approach, one that requires no external tools, is to temporarily change the RETURN statement of a measure so that it returns the value of an intermediate variable instead of the final result. When the variable contains a scalar value such as a number or a string, this is straightforward: you change the RETURN, observe the result in the report, and compare it with your expectations. We see this in the following example:

Measure in Sales table

```dax


Delta Avg 2 =

VAR CurrentValue = [Avg Transaction]

VAR ReferenceValue = CALCULATE ( [Avg Transaction], ALLSELECTED ( ) )

VAR CurrentDelta = CurrentValue - ReferenceValue

VAR Result = DIVIDE ( CurrentDelta, ReferenceValue )

RETURN ReferenceValue

```

That technique remains fully valid: read the previous article if you are not familiar with it yet.

The situation becomes more complex when the variable you want to inspect contains a table. A measure can only return a scalar value, so you cannot simply return a table variable. In the previous article, we used [[CONCATENATEX]] to convert a table into a string by manually specifying which columns to include and how to format them. However, this approach requires writing a specific [[CONCATENATEX]] expression for each table you want to inspect, choosing the columns, defining the separator, and adjusting the format every time. This is time consuming, especially during an active debugging session where you may need to inspect several variables in quick succession.

A full-featured DAX debugger is available in a commercial tool, [Tabular Editor 3](https://tabulareditor.com/), which provides step-by-step execution and variable inspection. Another tool you may find useful is [DAX Studio](https://daxstudio.org/), which is free. However, not all developers have access to these tools, and even those who do sometimes need a quick, lightweight technique that works directly in Power BI without opening another tool.

[[TOJSON]] and [[TOCSV]] offer exactly that. These two functions convert a table into a string (JSON or CSV format, respectively) without requiring you to specify the columns or the format. You pass the table variable, and the function produces a complete textual representation of its content. The result is a scalar string that a measure can return and that a visual can display in a report.

It is important to highlight that debugging a measure often requires inspecting its value in a specific filter context. For example, you might notice that a matrix shows an incorrect value for a specific cell, such as a particular combination of year and product category, or for one of the subtotals. In that case, displaying the debugging output in a card visual would not be sufficient, because a card only shows the value in the filter context of the visual, so you should use external slicers and the filter pane to reproduce the filters combination to investigate. A more effective approach is to use the debugging measure directly in a matrix, so that you can inspect the content of the table variable within the filter context where the incorrect result appears. This is a typical scenario: the total does not correspond to the expected value, and you need to see what the intermediate table contains for that specific cell.

This article describes the syntax and practical use of [[TOJSON]] and [[TOCSV]] for this purpose. We illustrate the technique with several examples and discuss the limitations you should be aware of.

## Inspecting table variables in a measure

Consider the following scenario. A measure builds an intermediate table in a variable (for example, using [[ADDCOLUMNS]], [[FILTER]], or [[SUMMARIZE]]) and then aggregates it to produce a final result, but the numbers are not what you expect. Here is the measure we use in this example, with two errors that are highlighted in the comments:

Measure in Sales table

```dax


Date MAX Start = 

VAR TargetAmount = [Max Daily Amount]

RETURN IF (

    NOT ISBLANK ( TargetAmount ),

    VAR DailySales = 

        ADDCOLUMNS ( 

            VALUES ( 'Date'[Date] ),

            "@DayAmount", [Sales Amount] 

        )

    VAR TargetDatesAmount =

        FILTER ( 

            DailySales, 

            -- Error 1: it should be TargetAmount instead of [Max Daily Amount]

            [@DayAmount] == [Max Daily Amount]

                && NOT ISBLANK ( [@DayAmount] ) 

        )

    VAR TargetDates =

        SELECTCOLUMNS ( 

            -- Error 2: it should be TargetDatesAmount instead of DailySales

            DailySales,

            'Date'[Date]

        )

    VAR Result =

        IF ( 

            COUNTROWS ( TargetDates ) = 1,

            FORMAT ( TargetDates, "mm/dd/yyyy" ),

            "Too many dates"

        )

    RETURN Result

)

```

The *Date [[MAX]] Start* measure should return the date when the *Max Daily Amount* was achieved, but it is not working.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-126.png)

We suspect that the intermediate table might contain unexpected rows or incorrect values, but we cannot see it directly – in the wild, you will not have the comments saying where the error is! We need a way to peek inside that variable, in the specific filter context where the result is wrong. The following measure inspects the *TargetDatesAmount* variable by using [[CONCATENATEX]]:

Measure in Sales table

```dax


Date MAX ConcatenateX = 

VAR TargetAmount = [Max Daily Amount]

RETURN IF (

    NOT ISBLANK ( TargetAmount ),

    VAR DailySales = 

-- ...

-- skipping implementation that is identical to Date MAX Start

-- ...

    RETURN 

        CONCATENATEX ( 

            TOPN ( 10, TargetDatesAmount ), 

            FORMAT ( 'Date'[Date], "mm/dd/yyyy" )

                & ": " & [@DayAmount] & ", "

        )

)

```

The result of *Date [[MAX]] ConcatenateX* shows that the values in *TargetDatesAmount* are not being filtered.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-122-scaled.png)

We would see the same result if we iterated *DailySales* instead of *TargetDatesAmount* in [[CONCATENATEX]], which indicates that the filter is ineffective. As described in the measure comments, we should replace the *Max Daily Amount* comparison with *TargetAmount* in the [[FILTER]] iteration. Similarly, by changing the [[CONCATENATEX]] iteration, we can see that *TargetDates* does not have the filtered rows once we fix the first bug, because we iterate over *DailySales* rather than *TargetDatesAmount*, as highlighted in the comments. Inspecting the variables helps us identify and fix these errors.

As we described in the introduction, the [[CONCATENATEX]] approach works, but it requires writing a custom expression for each table you want to inspect. With [[TOJSON]] and [[TOCSV]], you can achieve the same result with a single function call, and there is no need to specify the columns.

## Converting a table to a string with [[TOCSV]]

[[TOCSV]] converts a table into a string, formatted as comma-separated values. Because the result is a string, it can be returned by a measure and displayed in a report visual.

The syntax of [[TOCSV]] is:

```dax


TOCSV ( <Table>, [<MaxRows>], [<Delimiter>], [<IncludeHeaders>] )

```

The first argument is the table to convert. The optional *MaxRows* parameter controls how many rows are included in the output; its default value is 10. The *Delimiter* parameter specifies the column separator (the default is a comma), and *IncludeHeaders* determines whether the first line contains column names (the default is TRUE). To inspect a table variable, we temporarily change the RETURN expression of the measure so that it returns the [[TOCSV]] output instead of the original result, thus reducing the code from 5 lines using [[CONCATENATEX]] to just one line:

Measure in Sales table

```dax


Date MAX TOCSV = 

VAR TargetAmount = [Max Daily Amount]

RETURN IF (

    NOT ISBLANK ( TargetAmount ),

    VAR DailySales = 

-- ...

-- skipping implementation that is identical to Date MAX Start

-- ...

    RETURN 

        TOCSV ( TargetDatesAmount, 3 )

)

```

The output is a plain text representation of the table content. Each row appears on a separate line, and columns are separated by the chosen delimiter. This is typically enough to verify whether the table contains the expected rows and values. We reduced the output to three rows to limit the vertical space used in the following screenshot.![](https://cdn.sqlbi.com/wp-content/uploads/image3-110.png)

## Converting a table to a string with [[TOJSON]]

[[TOJSON]] works similarly to [[TOCSV]], but it produces a JSON-formatted string instead of a string formatted as comma-separated values. The syntax is simpler:

```dax


TOJSON ( <Table>, [<MaxRows>] )

```

The only optional parameter is *MaxRows*, which defaults to 10, the same as [[TOCSV]]. [[TOJSON]] does not support parameters for delimiters or header control because the JSON format has a fixed structure:

Measure in Sales table

```dax


Date MAX TOJSON = 

VAR TargetAmount = [Max Daily Amount]

RETURN IF (

    NOT ISBLANK ( TargetAmount ),

    VAR DailySales = 

-- ...

-- skipping implementation that is identical to Date MAX Start

-- ...

    RETURN 

        TOJSON ( TargetDatesAmount, 3 )

)

```

The JSON output contains three elements: a “header” array with column names, a “rowCount” field indicating the total number of rows in the original table (regardless of *MaxRows*), and a “data” array with the actual row values.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-104.png)

The “rowCount” field in the JSON output is particularly useful: it tells you how many rows the table actually contains, even when only the first few rows are displayed, as is the case in the screenshot above. This information is not available in the [[TOCSV]] output.

## Choosing between [[TOCSV]] and [[TOJSON]]

Both functions serve the same purpose in a debugging context. The choice between them largely comes down to personal preference and readability.

[[TOCSV]] produces a more compact output that is easier to read at a glance, especially for small tables with a few columns. [[TOJSON]] produces a more structured output that includes the row count and is easier to parse programmatically. In our experience, [[TOCSV]] is more practical for quick visual inspection during debugging. [[TOJSON]] is more useful when you need to know the total row count of the original table, or when you plan to copy the output into another tool for further analysis.

![](https://cdn.sqlbi.com/wp-content/uploads/image5-89-scaled.png)

Another difference between the two functions is when the table is empty: [[TOCSV]] always return a result, showing the column names in that case, whereas [[TOJSON]] does not return any result, which makes it hard to investigate the content of the table when it is empty. (Thank you Jan S for reporting it!)

## Understanding the *MaxRows* limitation

Both [[TOCSV]] and [[TOJSON]] default to returning only 10 rows. This is an intentional design choice: converting a large table to a string can produce a very long text, which is both hard to read and potentially expensive to compute. The default limit of 10 rows keeps the output manageable.

For debugging purposes, 10 rows are often sufficient. When we are verifying the structure of an intermediate table (like checking which columns are present, whether the values look correct, and whether unexpected rows appear), the first few rows usually provide enough evidence to identify the problem. We used only 3 rows in the example to maximize the visibility of the screenshots in the article, but we usually keep the default of 10.

However, there are scenarios where 10 rows are not enough. If the issue you are investigating only manifests further down in the table, or if you need to verify the complete content, you can increase the *MaxRows* parameter.

Be mindful that increasing *MaxRows* significantly can produce a very long string. A visual may truncate the output, and the measure evaluation can become slower. For most debugging sessions, a value between 10 and 20 is a reasonable range. If you need to inspect a table with hundreds or thousands of rows, consider using DAX Studio or Tabular Editor 3 instead, which are better suited for exploring large datasets.

It is also important to note that the sort order of the rows returned by [[TOCSV]] and [[TOJSON]] cannot be controlled directly. The functions return rows in whatever order the engine provides, which may not be deterministic. Indeed, the previous example comparing [[TOCSV]] and [[TOJSON]] outputs shows three days in January (January 3rd, 4th, and 11th, respectively) that are not the first three days available in the month. If row order is important for your investigation, you should sort the table explicitly before passing it to [[TOCSV]] or [[TOJSON]]. For example, you might wrap it in a [[TOPN]] expression with the desired sort order:

Measure in Sales table

```dax


Date MAX TOCSV Sorted = 

VAR TargetAmount = [Max Daily Amount]

RETURN IF (

    NOT ISBLANK ( TargetAmount ),

    VAR DailySales = 

-- ...

-- skipping implementation that is identical to Date MAX Start

-- ...

    RETURN 

        TOCSV ( 

            TOPN ( 3, TargetDatesAmount, 'Date'[Date], DESC )

        )

)

```

Measure in Sales table

```dax


Date MAX TOJSON Sorted = 

VAR TargetAmount = [Max Daily Amount]

RETURN IF (

    NOT ISBLANK ( TargetAmount ),

    VAR DailySales = 

-- ...

-- skipping implementation that is identical to Date MAX Start

-- ...

    RETURN 

        TOJSON ( 

            TOPN ( 3, TargetDatesAmount, 'Date'[Date], DESC )

        )

)

```

This way, the result includes the first three days with sales for each month, even though they are not sorted within the output produced by [[TOCSV]] and [[TOJSON]] (you can control the order in [[CONCATENATEX]], which requires additional parameters).![](https://cdn.sqlbi.com/wp-content/uploads/image6-81-scaled.png)

## A practical debugging workflow

Let us describe a typical debugging workflow that uses [[TOCSV]] to inspect intermediate variables.

Suppose we are developing a measure that computes a result through several steps, each stored in a variable. The result is not what we expect. Rather than guessing which step is wrong, we can systematically inspect each table variable by temporarily changing the RETURN statement. In the previous examples, we have seen several approaches to investigating the *TargetDatesAmount* variable by using [[CONCATENATEX]], [[TOCSV]], and [[TOJSON]]. Looking at the content produced, we locate and fix the first error, now testing the following measure:

Measure in Sales table

```dax


Date MAX Step 2 = 

VAR TargetAmount = [Max Daily Amount]

RETURN IF (

    NOT ISBLANK ( TargetAmount ),

    VAR DailySales = 

        ADDCOLUMNS ( 

            VALUES ( 'Date'[Date] ),

            "@DayAmount", [Sales Amount] 

        )

    VAR TargetDatesAmount =

        FILTER ( 

            DailySales, 

            -- Error 1 fixed

            [@DayAmount] == TargetAmount

                && NOT ISBLANK ( [@DayAmount] ) 

        )

    VAR TargetDates =

        SELECTCOLUMNS ( 

            -- Error 2: it should be TargetDatesAmount instead of DailySales

            DailySales,

            'Date'[Date]

        )

    VAR Result =

        IF ( 

            COUNTROWS ( TargetDates ) = 1,

            FORMAT ( TargetDates, "mm/dd/yyyy" ),

            "Too many dates"

        )

    RETURN 

        TOCSV ( TargetDatesAmount, 3 )

)

```

With this version of the measure, the *TargetDatesAmount* variable now has only one row per month and year, which indicates that the filter is working correctly.

![](https://cdn.sqlbi.com/wp-content/uploads/image7-69.png)

At this point, if we look at the result, we still see the same incorrect “Too many dates” sentence we had in the beginning. We must investigate more, so we return [[TOCSV]] applied to the next variable, *TargetDates*:

Measure in Sales table

```dax


Date MAX Step 3 = 

VAR TargetAmount = [Max Daily Amount]

RETURN IF (

    NOT ISBLANK ( TargetAmount ),

    VAR DailySales = 

-- ...

-- skipping implementation that is identical to Date MAX Step 2

-- ...

    RETURN 

        TOCSV ( TargetDates, 3 )

)

```

The result no longer includes *Sales Amount* computed for each date. However, we see three dates instead of one in each cell. If we used [[TOJSON]], we would see a larger “rowCount” in each cell. The filter we fixed in *TargetDatesAmount* does not apply to the next step. Why? By reviewing the code more closely, we notice that we referenced *DailySales* again instead of *TargetDatesAmount* when iterating over the table in [[SELECTCOLUMNS]]. We fix this reference and we test the code again:

Measure in Sales table

```dax


Date MAX Step 4 = 

VAR TargetAmount = [Max Daily Amount]

RETURN IF (

    NOT ISBLANK ( TargetAmount ),

    VAR DailySales = 

        ADDCOLUMNS ( 

            VALUES ( 'Date'[Date] ),

            "@DayAmount", [Sales Amount] 

        )

    VAR TargetDatesAmount =

        FILTER ( 

            DailySales, 

            -- Error 1 fixed

            [@DayAmount] == TargetAmount

                && NOT ISBLANK ( [@DayAmount] ) 

        )

    VAR TargetDates =

        SELECTCOLUMNS ( 

            -- Error 2 fixed

            TargetDatesAmount,

            'Date'[Date]

        )

    VAR Result =

        IF ( 

            COUNTROWS ( TargetDates ) = 1,

            FORMAT ( TargetDates, "mm/dd/yyyy" ),

            "Too many dates"

        )

    RETURN 

        TOCSV ( TargetDates, 3 )

)

```

At this point, the *TargetDates* variable has only one row per cell.

![](https://cdn.sqlbi.com/wp-content/uploads/image8-57.png)

We can revert to *Result* after the RETURN statement and see the correct report:

Measure in Sales table

```dax


Date MAX Fixed = 

VAR TargetAmount = [Max Daily Amount]

RETURN IF (

    NOT ISBLANK ( TargetAmount ),

    VAR DailySales = 

-- ...

-- skipping implementation that is identical to Date MAX Step 4

-- ...

    RETURN Result

)

```

![](https://cdn.sqlbi.com/wp-content/uploads/image9-49.png)

To recap, the process is the following: we replace the RETURN expression with [[TOCSV]] (or [[TOJSON]]) of the variable to inspect. We check the output, confirm whether that variable contains the expected content, and then move on to the next variable. Once we identify the step that produces the incorrect result, we can focus our analysis on that specific part of the measure.

After debugging is complete, we restore the original RETURN expression. The [[TOCSV]] or [[TOJSON]] call was never part of the measure logic: it was only a temporary lens used to inspect the calculation.

## Conclusions

[[TOJSON]] and [[TOCSV]] are simple functions with a specific and very practical use in everyday DAX development: they let us convert a table into a string, so that we can return it from a measure and inspect its content directly in a report visual. This makes them valuable debugging tools when we need to verify the content of intermediate table variables.

The default limit of 10 rows is adequate for most debugging scenarios, but it can be increased when needed. Be mindful that very large outputs can be difficult to read and potentially slow to compute. For large-scale data exploration, dedicated tools such as DAX Studio and Tabular Editor 3 remain the better options.

The debugging technique itself is straightforward: temporarily replace the RETURN expression of your measure with a [[TOCSV]] or [[TOJSON]] call targeting the variable you want to inspect. Check the output, identify the problem, fix the measure, and restore the original RETURN. It is an effective workflow that requires no external tools and works entirely within Power BI.

[[CONCATENATEX]]

Evaluates expression for each row on the table, then return the concatenation of those values in a single string result, seperated by the specified delimiter.

`CONCATENATEX ( <Table>, <Expression> [, <Delimiter>] [, <OrderBy_Expression> [, [<Order>] [, <OrderBy_Expression> [, [<Order>] [, … ] ] ] ] ] )`

[[TOJSON]]

Converts the records of a table into a JSON text.

`TOJSON ( <Table> [, <MaxRows>] )`

[[TOCSV]]

Converts the records of a table into a CSV (comma-separated values) text.

`TOCSV ( <Table> [, <MaxRows>] [, <Delimiter>] [, <IncludeHeaders>] )`

[[ADDCOLUMNS]]

Returns a table with new columns specified by the DAX expressions.

`ADDCOLUMNS ( <Table>, <Name>, <Expression> [, <Name>, <Expression> [, … ] ] )`

[[FILTER]]

Returns a table that has been filtered.

`FILTER ( <Table>, <FilterExpression> )`

[[SUMMARIZE]]

Creates a summary of the input table grouped by the specified columns.

`SUMMARIZE ( <Table> [, <GroupBy_ColumnName> [, [<Name>] [, [<Expression>] [, <GroupBy_ColumnName> [, [<Name>] [, [<Expression>] [, … ] ] ] ] ] ] ] )`

[[MAX]]

Returns the largest value in a column, or the larger value between two scalar expressions. Ignores logical values. Strings are compared according to alphabetical order.

`MAX ( <ColumnNameOrScalar1> [, <Scalar2>] )`

[[TOPN]]

Returns a given number of top rows according to a specified expression.

`TOPN ( <N_Value>, <Table> [, <OrderBy_Expression> [, [<Order>] [, <OrderBy_Expression> [, [<Order>] [, … ] ] ] ] ] )`

[[SELECTCOLUMNS]]

Returns a table with selected columns from the table and new columns specified by the DAX expressions.

`SELECTCOLUMNS ( <Table> [[, <Name>], <Expression> [[, <Name>], <Expression> [, … ] ] ] )`
