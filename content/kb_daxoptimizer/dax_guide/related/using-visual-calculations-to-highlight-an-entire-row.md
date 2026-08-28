---
title: "Using visual calculations to highlight an entire row"
url: "https://www.sqlbi.com/articles/using-visual-calculations-to-highlight-an-entire-row"
published: "2026-04-06"
updated: 
source: "sqlbi.com"
---

# Using visual calculations to highlight an entire row

> 发布：2026-04-06

When it comes to visuals, users may want to specific cells highlighted in order to spot important information quickly. While browsing the forums, we came across an interesting requirement that can easily be solved with a DAX measure: highlight an entire row based on the value in the last column of the visual only. In our example, we highlight Wide World Importers because it has the maximum value (71,904.98) in the last year (2026).

![](https://cdn.sqlbi.com/wp-content/uploads/image1-129.png)

## Introducing the measure solution

The scenario can be easily solved with a regular measure that computes the last visible year, then the values of different brands in that year, and finally searches for the brands with the maximum value (there may be more than just one) and produces “Yellow” for those brands only:

Measure in Sales table

```dax


Color =

VAR LastYear =

    CALCULATE ( MAX ( 'Date'[Year] ), ALLSELECTED () )

VAR BrandsInLastYear =

    CALCULATETABLE (

        ADDCOLUMNS ( VALUES ( 'Product'[Brand] ), "@Sales", [Sales Amount] ),

        ALLSELECTED ( 'Product'[Brand] ),

        'Date'[Year] = LastYear

    )

VAR MaxValueInLastYear =

    MAXX ( BrandsInLastYear, [@Sales] )

VAR BrandsWithMaxValueInLastYear =

    SELECTCOLUMNS (

        FILTER ( BrandsInLastYear, [@Sales] = MaxValueInLastYear ),

        'Product'[Brand]

    )

VAR CurrentBrand =

    SELECTEDVALUE ( 'Product'[Brand] )

VAR Result =

    IF ( CurrentBrand IN BrandsWithMaxValueInLastYear, "Yellow" )

RETURN

    Result

```

A good DAX developer writes this code and obtains the desired result. However, the code has a couple of drawbacks worth pointing out:

- Using a model measure to change the behavior of a visual is a bit overkill. Every visual has specific requirements that necessitate creating multiple measures whose sole purpose is to alter the aesthetics of the report.
- The measure works fine if the visual includes the year on the columns and the brand on the rows. Changing the structure of the visual, for example, using the *Product[Category]* on the columns, requires also changing the measure code.

## Implementing a visual calculation

It would be much more convenient to embed the aesthetic changes in a visual calculation, so that the model does not become messy. Using a visual calculation requires less intuitive code because it requires navigating the visual lattice using [[EXPAND]] and [[COLLAPSE]]. On the other hand, the ability to reference ROWS and COLUMNS, and to use nice functions like [[LAST]], simplifies some of the calculations:

Visual calculation

```dax


Color = 

VAR LastYear = 

    CALCULATE ( 

        CALCULATE ( 

            LAST ( [Year], COLUMNS ), 

            EXPAND ( COLUMNS ) 

        ),

        COLLAPSEALL( ROWS COLUMNS ) 

    )

VAR RowsInLastYear = 

    CALCULATETABLE ( 

        CALCULATETABLE ( 

            FILTER ( 

                ROWS COLUMNS,

                [Year] = LastYear

            ),

            EXPAND ( ROWS COLUMNS )

        ),

        COLLAPSEALL ( ROWS COLUMNS )

    )

VAR MaxValueInLastYear = MAXX ( RowsInLastYear, [Sales Amount] )

VAR BrandsWithMaxValueInLastYear = 

    SELECTCOLUMNS ( 

        FILTER ( RowsInLastYear, [Sales Amount] = MaxValueInLastYear ), 

        "Brand", [Brand] 

    )

VAR CurrentBrand = [Brand]

RETURN

    IF ( CurrentBrand IN BrandsWithMaxValueInLastYear, "Yellow" )

```

The main advantage of using a visual calculation is that the code is in the visual rather than in the model. Therefore, if the visual is copied to a different page and reorganized, the visual calculation code can be modified to work in the new visual.

However, the solution is not optimal yet. The visual calculation needs to reference the column names in multiple places. For example, if one uses a different measure, they need to modify the code. If *Category* is in the rows rather than *Brand*, the code needs to be adapted again. The chances of making mistakes are quite high.

In this scenario, functions are king. A good way to think about the scenario is to write a function that takes a table representing the matrix content and returns the name of the brand (or whatever column is required) with the maximum value for the year (or, again, whatever column is required). The visual calculation will only need to build the matrix, pass it down to the function along with the names of the columns to use, and then decide the color to use.

A possible implementation of the function is the following:

Function

```dax


RowHeaderOfLastCol = 

    ( 

        matrix : TABLE, 

        colHeader : COLUMNREF, 

        rowHeader : COLUMNREF, 

        valueExpr : EXPR 

    ) => 

    VAR LastColHeader = MAXX ( matrix, colHeader )

    VAR RowsInLastCol = 

        FILTER (

            matrix,

            colHeader = LastColHeader

        )

    VAR MaxValueInLastCol = MAXX ( RowsInLastCol, valueExpr )

    VAR RowWithMaxValueInLastCol = 

        FILTER (

            RowsInLastCol, 

            valueExpr = MaxValueInLastCol

        )

    VAR Result = SELECTCOLUMNS ( RowWithMaxValueInLastCol, "rowHeader", rowHeader )

    RETURN Result

```

As you may notice, the function no longer references *Brand*, *Sales Amount,* and *Year*. The function is, by its nature, generic. It receives a table representing the matrix content and searches for the row headers that contain the maximum value in the last column.

The visual calculation is much simpler, as it only needs to build the table containing the matrix and then invoke the function:

Visual calculation

```dax


Color = 

VAR BestBrands = 

    CALCULATETABLE ( 

        CALCULATETABLE ( 

            RowHeaderOfLastCol ( ROWS COLUMNS, [Year], [Brand], [Sales Amount] ),

            EXPAND ( ROWS COLUMNS )

        ),

        COLLAPSEALL ( ROWS COLUMNS )

    )

VAR CurrentBrand = [Brand]

RETURN 

    IF ( CurrentBrand IN BestBrands, "Yellow" )

```

The main advantage is that the reference to the column names appears in just one line: the function call. The remaining part of the visual calculation does not need to be adapted for a different visual, thus making it easier to produce different implementations of the same business logic.

As an example, the following is a visual calculation that highlights rows in a matrix that contains the category as the top level, and the brand as the second level only:

```dax


Color = 

VAR BestBrands = 

    CALCULATETABLE ( 

        CALCULATETABLE ( 

            RowHeaderOfLastCol ( VALUES (ROWS COLUMNS ), [Year], [Brand], [Sales Amount] ),

            EXPAND ( [Year] ),

            EXPAND ( [Brand] )

        ),

        COLLAPSE ( ROWS COLUMNS )

    )

VAR CurrentBrand = [Brand]

RETURN 

    IF ( CurrentBrand IN BestBrands, "Yellow" )

```

The result is that brands are highlighted locally in the current category.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-125.png)

## Conclusions

In its simplicity, this example shows several useful techniques in the management of your code, and also several DAX features that are not trivial, like using ROWS COLUMNS to create a table with the content of the matrix, the navigation in the lattice, and the capability of passing columns of the virtual table down to functions.

Going into the many details would take much longer, and that type of content would not be suitable for a short article and its related video. If you are interested in learning more about these topics, we have published a longer video, [How to navigate the lattice of visual calculations](https://www.sqlbi.com/tv/how-to-navigate-the-lattice-of-visual-calculations/), available to all our [SQLBI+](https://www.sqlbi.com/p/plus/) subscribers.

[[EXPAND]]

Retrieves a context with added levels of detail compared to the current context. If an expression is provided, returns its value in the new context, allowing for navigation in hierarchies and calculation at a more detailed level.

`EXPAND ( [<Expression>] [, <Axis>] [, <Column> [, <Column> [, … ] ] ] [, <N>] )`

[[COLLAPSE]]

Retrieves a context with removed detail levels compared to the current context. With an expression, returns its value in the new context, allowing navigation up hierarchies and calculation at a coarser level of detail.

`COLLAPSE ( [<Expression>] [, <Axis>] [, <Column> [, <Column> [, … ] ] ] [, <N>] )`

[[LAST]]

The Last function retrieves a value in the Visual Calculation data grid from the last row of an axis.

`LAST ( <Column> [, <Axis>] [, <OrderBy>] [, <Blanks>] [, <Reset>] )`
