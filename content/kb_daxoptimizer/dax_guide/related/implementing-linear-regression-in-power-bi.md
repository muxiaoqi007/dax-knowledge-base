---
title: "Implementing linear regression in Power BI"
url: "https://www.sqlbi.com/articles/implementing-linear-regression-in-power-bi"
published: "2023-06-05"
updated: 
source: "sqlbi.com"
---

# Implementing linear regression in Power BI

> 发布：2023-06-05

[[LINEST]] and [[LINESTX]] are two DAX functions that calculate a linear regression by using the Least Squares method. Both functions return multiple values, represented in a table that has a single row and one column for each of the values returned.

[[LINEST]] gets column references as arguments, whereas [[LINESTX]] explicitly iterates over the table provided in the first argument and executes the other arguments in a row context. Internally, [[LINEST]] invokes [[LINESTX]] and provides to it the table that contains the column references specified in the [[LINEST]] arguments. This article describes the more generic function [[LINESTX]].

We start our example by analyzing for all transactions of our Contoso database, the average price for various quantities purchased.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-56.png)

Through the linear regression, we want to obtain the following result.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-53.png)

In our simple scenario, the goal is to produce the **slope** and **intercept** parameters for the following formula:

```dax


Y = X * slope + intercept

```

We want to compute the linear regression by considering all the values of *Sales[Quantity]* displayed in the chart. The [[LINESTX]] function is the best candidate, as it allows us to specify the number of data points used to compute the linear regression.

The first argument of [[LINESTX]] is the table to iterate for the calculation: for each row, there is an expression evaluated for the Y-axis and one or more expressions evaluated for the X-axis. In this article, we consider the simplest case where we have a single expression for the X-axis. This is the [[LINESTX]] syntax to compute the *slope* and *intercept* parameters of the linear regression for our chart:

```dax


LINESTX (

    ALLSELECTED ( Sales[Quantity] ), -- Table with datapoints to iterate

    [Avg Price],                     -- Expression for the Y-axis

    Sales[Quantity]                  -- Expression for the X-axis

)

```

[[LINESTX]] returns two values: **slope** and **intercept**. It actually returns more columns, though we only need these first two columns for now. We are used to aggregation functions returning a single scalar value. [[LINESTX]] can return multiple values by returning a table that has one row and multiple columns. Indeed, we can execute the following DAX query:

```dax


DEFINE

    MEASURE Sales[Avg Price] = [Sales Amount] / [Total Quantity]

EVALUATE

LINESTX (

    ALL ( Sales[Quantity] ),

    [Avg Price],

    Sales[Quantity]

)

```

| Slope1 | Intercept | StandardErrorSlope1 | StandardErrorIntercept | CoefficientOfDetermination | StandardError | FStatistic | DegreesOfFreedom | RegressionSumOfSquares | ResidualSumOfSquares |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 0.12 | 283.60 | 1.43 | 8.84 | 0.00 | 12.95 | 0.01 | 8 | 1.23 | 1,340.92 |

The single row returned by the query has more columns than just **slope** and **intercept**.

[[LINEST]] and [[LINESTX]] return more statistical information about the result of the [Least Squares algorithm](https://en.wikipedia.org/wiki/Least_squares). If you are from a statistics background, you probably already know these additional parameters. Otherwise, you may find more information at [Wolfram MathWorld](https://mathworld.wolfram.com/LeastSquaresFitting.html). In both cases, our goal here is to explain how to consume the result of [[LINESTX]] in DAX.

Because in a DAX formula you are likely to use several of the values returned by [[LINESTX]] (slope and intercept in our case), the most efficient technique is to store the result in a variable and access each value by using [[SELECTCOLUMNS]]. Here is the definition of the *LinearRegression* measure we used to draw the linear regression line in our chart:

```dax


LinearRegression = 

VAR line =

    LINESTX (

        ALL ( Sales[Quantity] ),

        [Avg Price],

        Sales[Quantity]

    )

VAR slope = SELECTCOLUMNS ( line, [Slope1] )

VAR intercept = SELECTCOLUMNS ( line, [Intercept] )

VAR x = SELECTEDVALUE ( Sales[Quantity] )

VAR y = x * slope + intercept

RETURN y

```

The *intercept* variable retrieves the value of the *Intercept* column. The result of [[SELECTCOLUMNS]] is a table with one row (because [[LINESTX]] returns only one row) and one column (because we specify only one column argument). Thanks to the automatic conversion provided by DAX, when we use the *slope* and *intercept* variables in the final calculation for y, these tables with one row and one column are automatically converted into the corresponding scalar value. In other languages, we would have written the code this way:

```dax


VAR slope = line[Slope1]

VAR intercept = line[Intercept]

```

However, DAX does not provide the column reference syntax to access the columns of a variable that includes a table. Therefore, we need the longer syntax provided by [[SELECTCOLUMNS]]. As it is not intuitive the first time, an entire article was well deserved for this technique alone!

Another detail that is important to explain is why we used “Slope1” for the column name to retrieve the slope value instead of just using “Slope”. The reason is that [[LINEST]] and [[LINESTX]] are functions that accept several series of values for the X-axis, generating a formula that has a slope value for each X-axis series of values. The equation for the line defined is the following:

```dax


Y = X1 * Slope1 + X2 * Slope2 + … + Xn * SlopeN + Intercept

```

Because we used only one value for the X-axis argument, we only need *Slope1*. When you write a formula, you know how many Slope parameters to use because it corresponds to the number of X-axis values provided to [[LINESTX]].

Creating a linear regression in DAX is now much easier [than it used to be](https://xxlbi.com/blog/simple-linear-regression-in-dax/) (also [through calculation groups](https://sergiomurru.com/2021/07/29/simple-linear-regression-in-dax-with-calculation-groups/)), you just have to pay attention to the correct use of variables, which are also required to avoid multiple evaluations of the same [[LINESTX]] function.

[[LINEST]]

Uses the Least Squares method to calculate a straight line that best fits the data, then returns a table describing the line. The equation for the line is of the form: y = m1\*x1 + m2\*x2 + … + b.

`LINEST ( <ColumnY>, <ColumnX> [, <ColumnX> [, … ] ] [, <Const>] )`

[[LINESTX]]

Uses the Least Squares method to calculate a straight line that best fits the data, then returns a table describing the line. The data result from expressions evaluated for each row in a table.

`LINESTX ( <Table>, <ExpressionY>, <ExpressionX> [, <ExpressionX> [, … ] ] [, <Const>] )`

[[SELECTCOLUMNS]]

Returns a table with selected columns from the table and new columns specified by the DAX expressions.

`SELECTCOLUMNS ( <Table> [[, <Name>], <Expression> [[, <Name>], <Expression> [, … ] ] ] )`
