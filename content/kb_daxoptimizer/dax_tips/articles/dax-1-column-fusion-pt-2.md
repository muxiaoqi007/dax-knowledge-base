---
title: "DAX 1-Column Fusion Pt 2"
url: "https://dax.tips/2020/06/24/dax-1-column-fusion-pt-2"
published: "2020-06-24"
updated: "2020-06-24"
source: "dax.tips"
---

I received feedback on my recent blog post on [[dax-1-column-fusion|single-column fusion]] asking about a curious syntax used in the solution. I promised I would do a deeper dive on why it works in my next blog, so here it is. This article is also a fun reminder/refresher on how iterator functions work in Power BI.

The PBIX file used for this article can be [downloaded here](https://1drv.ms/u/s!AtDlC2rep7a-zl8kCAblIcYH1yg6?e=7l0ZgF), and the basic pattern we are drilling down on is as follows:

```
Base Measure = COUNTROWS('fact Sale')
```

```
Package - Bag = 
    MAXX(
        ALL('fact Sale'[Package]), 
        [base measure] * ('fact Sale'[Package]="Bag")
    )
```

When used in a Power BI visual, along with a grouping on Calendar Year, we get the following results:

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/06/B1-2.png?resize=406%2C291&ssl=1)

The column I’m interested in is **Package – Bag** and let’s assume the values shown are correct for the dataset.

You may notice the [Package – Bag] measure uses the \* (multiplication) operator in line 4 between a measure and what looks like a column filter.

The [base measure] performs a simple [COUNTROWS](https://docs.microsoft.com/en-us/dax/countrows-function-dax) aggregation and returns an INTEGER. This side of the equation makes sense, so let’s see what is going on with the DAX on the right-hand side of the \* operator? The filter statement is encompassed with parenthesis, which automatically converts the expression to a TRUE/FALSE boolean value – which is then implicitly converted to an INTEGER value of either 0 or 1. It can’t be anything else.

The result of this double conversion is that we end up with a INTEGER \* INTEGER to produce the number we see in the visual.

So far, so good!

### Debug Measure 1

We now know our value is going to be the result of an INTEGER \* INTEGER operation. So, let’s create a debug measure to try and understand what value each INTEGER produces.

Let’s add the following debug measure to the model – note the removal of the \* operator along with the right-hand INTEGER value.

```
Package - Bag (Debug 1) = 
    MAXX(
        ALL('fact Sale'[Package]), 
        [base measure] 
    )
```

When added to the same visual produces the following :

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/06/B2-1.png?resize=606%2C324&ssl=1)

The values shown in the new column are the correct unfiltered values for the count of rows over the ‘Fact Sale’ table by calendar year. This result means, if we look at the other side of the \* operator, we should hopefully see how the original measure arrives at the correct value for each row.

### Debug Measure 2

Let’s create a second debug measure that focuses on the right-hand side of our original equation. I have added the [CONVERT](https://docs.microsoft.com/en-us/dax/convert-function-dax) function around the filter to performs the same conversion as the original measure.

```
Package - Bag (Debug 2) = 
    MAXX(
        ALL('fact Sale'[Package]), 
        CONVERT('fact Sale'[Package]="Bag",INTEGER)
    )
```

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/06/B3-1.png?resize=745%2C304&ssl=1)

This new measure produces a 1 in every row, which appears confusing because this implies that for the year 2016, the original measure is multiplying 27,245 \* 1 to arrive at 1,036.

My math isn’t the best, but I can tell this doesn’t seem right, and we need to dig deeper.

### Iterator Functions

The original calculation uses the [MAXX](https://docs.microsoft.com/en-us/dax/maxx-function-dax) Iterator function, and perhaps this function plays a role in producing the correct value. There isn’t much else in the measure left to look.

All iterator functions in DAX have the same parameter signature for the first two parameters. The first parameter is always a table, while the second parameter is always a DAX expression. Depending on the iterator function, there may be three or more parameters customized to the specific iterator function.

Iterator functions in DAX perform a series of loops. The number of loops is determined by the number of rows in the table passed as the first parameter. Not only that, but the <expresssion> used in the 2nd parameter can also be influenced by values in the row currently being iterated.

Therefore, each cell in the column using our original [Package – BAG] measure performs a number of loops as part of the [MAXX](https://docs.microsoft.com/en-us/dax/maxx-function-dax) function. The first parameter of our [MAXX](https://docs.microsoft.com/en-us/dax/maxx-function-dax) function uses the [ALL](https://docs.microsoft.com/en-us/dax/all-function-dax) function over the ‘Fact Sale'[Package] column to return the following distinct list of 4 values:

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/06/B4-1.png?resize=580%2C287&ssl=1)

So, we now know the [MAXX](https://docs.microsoft.com/en-us/dax/maxx-function-dax) function will perform four loops and evaluate four separate DAX expressions and return a value from the loop that produced the highest value.

How can we be sure this is still doing exactly what we need?

My preferred approach to debugging iterator functions is to use the [CONCATENATEX](https://docs.microsoft.com/en-us/dax/concatenatex-function-dax) function, by substituting the iterator function in use (MAXX) with [CONCATENATEX](https://docs.microsoft.com/en-us/dax/concatenatex-function-dax) to output info from inside each loop as text visually.

Let’s add one final debug measure to the model.

### Debug Measure 3

The final measure to add should be as follows:

```dax
Package - Bag (Debug 3) = 
    CONCATENATEX(
        ALL('fact Sale'[Package]), 

        COMBINEVALUES(
            " " , 
            " (" &amp; CALCULATE(MIN('Fact Sale'[Package])) &amp; ")=" ,
            [base measure] , 
            "*" , 
            CONVERT('fact Sale'[Package]="Bag",INTEGER),
            " , "
            )    
        )

```

While the measure looks long, the first three lines of the measure are the same as our original measure. Lines 5 through 12 use the [COMBINEVALUES](https://docs.microsoft.com/en-us/dax/combinevalues-function-dax) function to build a string of text showing all the components relevant for the current loop.

Line 8 shows the value produced by [Base Measure] in each loop, and line 10 shows the value of the BOOLEAN value in each step.

Once this measure gets created in the model, it can be added to a table visual to show the following:

![](https://i1.wp.com/dax.tips/wp-content/uploads/2020/06/B5.png?fit=1000%2C235&ssl=1)

The debug measure provides an excellent visual representation of the iterator. By focusing on the 2016 row, we can see the iterator performed four loops (highlighted by four red boxes). This result is what we expected. Within each loop, our original multiplication expression gets evaluated, and it’s these numbers that produce a result per iteration.

The equations in the first three iterations all produce a zero value, so its the expression in the fourth and final iteration of 1,036 \* 1 = 1,036 that gets returned by the [MAXX](https://docs.microsoft.com/en-us/dax/maxx-function-dax) function.

Note that for this measure, all the other calendar years our measure produces a zero for all loops of the [MAXX](https://docs.microsoft.com/en-us/dax/maxx-function-dax) function.

### Pseudo Logic

In other languages, the [MAXX](https://docs.microsoft.com/en-us/dax/maxx-function-dax) function might be loosely represented as something close to the following :

```dax
function MAXX (table t , expression e)
    {
    var maxValue
    foreach (row in t)
        {
        if( e > maxValue )
            maxValue = e
        }
    return maxValue
    }

```

### Summary

Iterator functions can be powerful additions to your calculations. They can help solve complex challenges such and ranking, and I strongly recommend using the [CONCATENATEX](https://docs.microsoft.com/en-us/dax/concatenatex-function-dax) function to help debug the two key elements of any iterator function.

- How many loops does the iterator perform when used in your visual
- The value produced by the expression by each loop.

Sometimes you discover the expression is not producing what you expect for each loop and adding a CALCULATE, or filter modifier can help you solve any issue you have.

4.9
15
votes

Article Rating
