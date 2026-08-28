---
title: "Analyzing the performance impact of visual calculations"
url: "https://www.sqlbi.com/articles/analyzing-the-performance-impact-of-visual-calculations"
published: "2026-07-28"
updated: 
source: "sqlbi.com"
---

# Analyzing the performance impact of visual calculations

> 发布：2026-07-28

The goal of visual calculations is to simplify some reports and calculations, rather than to optimize performance. However, it is common sense that – in some scenarios – visual calculations can bring some benefit from the performance point of view.

The main idea is that a report may precompute some values and then, to further elaborate on them, it may use the content of the virtual table rather than recompute the values multiple times.

Imagine we want to compute a report containing the distinct count of customers by year, as well as the growth in percentage between the current year and the previous years.

![](https://cdn.sqlbi.com/wp-content/uploads/C0298-1.png)

The measure required is straightforward:

Measure in Sales table

```dax


YOY % =

VAR CY = [# Customers]

VAR PY =

    CALCULATE ( [# Customers], SAMEPERIODLASTYEAR ( 'Date'[Date] ) )

VAR Result =

    DIVIDE ( CY - PY, PY )

RETURN

    Result

```

We chose [[DISTINCTCOUNT]] on purpose: we wanted a measure that is non-aggregatable and heavy in terms of computational requirements. When executed on the version of Contoso with 23M rows in *Sales*, the report requires around 13 seconds to execute, and this is not surprising at all. Before looking at the details in the server timings, let us reason as to why the measure is expected to be slow.

First of all, not only is [[DISTINCTCOUNT]] a heavy operation to perform, but it is also non-additive – as such, it requires multiple scans of the fact table to compute. Secondly, time intelligence calculations require complex reasoning that is difficult to optimize. The DAX engine first computes the distinct count of customers by year. Then for each year, it determines the previous year; and to compute the previous year’s distinct count, it then runs another query against the database. In other words, each cell requires its own query to retrieve the value of the previous year.

Indeed, when looking at the details, we see confirmation of our reasoning: there are many storage engine queries, each computing the distinct count for one year.

![](https://cdn.sqlbi.com/wp-content/uploads/C0298-2.png)

As humans, the first consideration that comes to mind is quite simple: why compute the value of the measure on a single year when the engine has already computed the value of the measure grouped by year? The value is already there; just use it without needing to recalculate it, right? True, but this is a very human behavior: we see the yearly value because the matrix is sliced by year; therefore, we cut corners and use the previously-computed value. DAX is more generic; it needs to work no matter what we slice by, which is why it uses a slower, yet more generic algorithm.

However, at the end of the day, we still feel that something can be improved. If the value is already there, there should be a way to use it with no further calculation. Indeed, a visual calculation does exactly this. When using visual calculations, the engine first computes the source query (that is, a query that retrieves all the values computed by model measures), and then the next step of calculation happens on the virtual table, with no further need to query the data model.

The same algorithm, with a visual calculation, is the following:

Visual Calculation

```dax


YOY % =

VAR CY = [# Customers]

VAR PY =

    PREVIOUS ( [# Customers], COLUMNS )

VAR Result =

    DIVIDE ( CY - PY, PY )

RETURN

    Result

```

By using [[PREVIOUS]], we are asking DAX to retrieve the value of *# Customers* in the previous column, with no need to invoke another query on the database.

The version using the visual calculation is significantly faster.

![](https://cdn.sqlbi.com/wp-content/uploads/C0298-3.png)

6 seconds rather than the previous 13 seconds. Not only is it faster, but by analyzing the storage engine queries, we observe a much more reasonable behavior. The engine retrieves the base measure by the different aggregation levels – it requires subtotals, which is why there are multiple storage engine queries – and then the remaining part of the calculation is all in the formula engine. Because the virtual table is rather small, the cost to the formula engine is quite low.

If we had used *Sales Amount,* an additive and lighter measure, rather than a distinct count, the result would be similar, but the timings would be so quick – with the query running in around 100 milliseconds – that we would have to use a much larger database to get a feeling for the behavior. Distinct count is used only to make the scenario clearer and to avoid optimizations specific to additive measures.

So far, we have seen one side of the coin: visual calculations improve performance when there is a small number of basic heavy calculations on top of which we need to perform further elaboration. The good news is that this happens pretty frequently, so giving visual calculation a try is a good step in building reports. However, there is another side of the coin to investigate: is it possible that visual calculations slow down performance? Unfortunately, the answer is yes.

When a visual calculation is added to a visual, the engine adds the VISUAL SHAPE structure to the source table so that it can navigate in the hierarchies defined by the visual (ROWS and COLUMNS). Adding VISUAL SHAPE forces the table to undergo a densification process.

Densification adds to the source table all the combinations of values that may be present, but that were skipped as part of the optimizations of [[SUMMARIZECOLUMNS]]. We describe the densification process in more detail in the SQLBI+ whitepaper [Understanding Visual Calculations in DAX](https://www.sqlbi.com/whitepapers/understanding-visual-calculations-in-dax/), and we demonstrate it further in the [corresponding SQLBI+ video course](https://www.sqlbi.com/learn/understanding-visual-calculations-in-dax/).

For the sake of this article, it suffices to think of densification as a step that adds rows to the virtual table to represent all combinations of values across rows and columns. In our example, there are 11 brands and 10 years; therefore, the source table can contain a maximum of 110 rows (not including subtotals, which we ignore to simplify the scenario). All 110 cells contain a value; therefore, [[SUMMARIZECOLUMNS]] returns all of them. However, if there were no purchases for one of the brands within one of the years, the corresponding cell in the matrix would be blank, and the original virtual table returned by [[SUMMARIZECOLUMNS]] would not even include that row, because it would have a useless [[BLANK]] value. It is worth remembering that [[SUMMARIZECOLUMNS]] does not return rows where all the measures evaluate to [[BLANK]]. During the densification process, DAX adds that row back to accommodate any visual calculation results that may be non-blank.

In other words, [[SUMMARIZECOLUMNS]] has a specific optimization to reduce the number of rows returned to avoid processing empty rows. However, in visual calculations, the engine needs to recreate those rows, albeit temporarily, to guarantee a correct evaluation of the visual calculations themselves. If the virtual table is very large, the densification process can take a long time, thereby degrading the report’s performance significantly.

To demonstrate this, we use *Sales Amount* and the *YOY %* of the sales amount (if we were to use [[DISTINCTCOUNT]], the timings would just be too large to make the tests viable), and we slice by *Store[Name]*, *Product[Name]*, and *Date[Year]*.

![](https://cdn.sqlbi.com/wp-content/uploads/C0298-4.png)

There are 67 stores, 2,517 products, and 10 years. The full source table is now much larger: 1,686,390 rows in total. Because there are so many cells to compute, the report with the measure is much slower than the one we used earlier, and the storage engine queries also take much longer to execute.

![](https://cdn.sqlbi.com/wp-content/uploads/C0298-5.png)

The report using the measure is running in 15.6 seconds, with a significant amount of time spent in the formula engine processing [[SAMEPERIODLASTYEAR]] across many combinations of values.

In the server timings, it is worth noting line 2, where 1,318,524 rows were processed. That is the original [[SUMMARIZECOLUMNS]]. Line 8 computes 16,458,828 rows and aggregates sales by date, so to compute the [[SAMEPERIODLASTYEAR]] in the formula engine.

Despite looking like a bad result, it is not. Not only is it not bad, but it is also the result of a combination of optimizations that aim to reduce the number of rows processed by both Power BI and [[SUMMARIZECOLUMNS]].

The effect of these optimizations vanishes when using a visual calculation. When using visual calculations, the DAX engine must materialize the full result of [[SUMMARIZECOLUMNS]] and then extend it during the densification step to handle missing values. On large virtual tables, this takes a lot of time.

These are the server timings of the visual calculation version of the same report.

![](https://cdn.sqlbi.com/wp-content/uploads/C0298-6.png)

The version with visual calculations is now four times slower than the version based on the measure. There are no more multiple queries to the storage engine (the engine only needs to retrieve the most granular data for the sales amount; everything else is computed from this raw dataset), but the densification step takes a long time, resulting in poor performance.

With different database sizes, the numbers are different. With the small database that you can download with this article, the difference looks even larger: it goes from around 40 milliseconds for the measure-based report to more than 400 milliseconds for the visual calculation. The reason is that the storage engine becomes irrelevant, which somewhat distorts the results.

## Conclusions

Visual calculations are a powerful tool. When it comes to making some calculations easier, they are just great. However, do not assume they are always faster than measure-based reports, as they compute values only once. We performed several other tests, and our conclusion is that what matters most is the size of the virtual table. With small virtual tables, visual calculations are great. As soon as the virtual table grows, performance can be at serious risk. Be mindful that measure-based reports are also optimized to compute only what is visible in the matrix. Therefore, if some levels are not expanded, they are not computed. With visual calculations, it does not matter whether the matrix is fully expanded or not; the engine must always produce the full densified virtual table at the leaf level.

As always, when it comes to performance, you should perform extensive testing. Your model, your measures, and your report may all have different characteristics from our demo model. Hence, your mileage may vary widely.

[[DISTINCTCOUNT]]

Counts the number of distinct values in a column.

`DISTINCTCOUNT ( <ColumnName> )`

[[PREVIOUS]]

The Previous function retrieves a value in the previous row of an axis in the Visual Calculation data grid.

`PREVIOUS ( <Column> [, <Steps>] [, <Axis>] [, <OrderBy>] [, <Blanks>] [, <Reset>] )`

[[SUMMARIZECOLUMNS]]

Create a summary table for the requested totals over set of groups.

`SUMMARIZECOLUMNS ( [<GroupBy_ColumnName> [, [<FilterTable>] [, [<Name>] [, [<Expression>] [, <GroupBy_ColumnName> [, [<FilterTable>] [, [<Name>] [, [<Expression>] [, … ] ] ] ] ] ] ] ] ] )`

[[BLANK]]

Returns a blank.

`BLANK ( )`

[[SAMEPERIODLASTYEAR]]

Context transition

Returns a set of dates in the current selection from the previous year.

`SAMEPERIODLASTYEAR ( <Dates> )`
