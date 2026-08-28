---
title: "Computing New Customers in DAX"
url: "https://www.sqlbi.com/articles/computing-new-customers-in-dax"
published: "2020-11-10"
updated: 
source: "sqlbi.com"
---

# Computing New Customers in DAX

> 发布：2020-11-10

**UPDATE 2020-11-10**: You can find **more complete detailed and optimized examples for this calculation in the [DAX Patterns: New and returning customers](https://www.daxpatterns.com/new-and-returning-customers/) article+video** on [daxpatterns.com](https://www.daxpatterns.com/).

Computing new and returning customers is one of my preferred formulas (along with event in progress such as open orders), just because it is very hard to compute it in an efficient way. Over time, I tried several approaches, the best of which we published in a [DAX Pattern here](http://www.daxpatterns.com/new-and-returning-customers/). Now it is time to learn a new, incredibly fast approach.

In the Optimizing DAX workshop, computing efficiently new customers is one of the many optimization exercises. During one of these courses, in Amsterdam, I was showing a simplified version of my best pattern (of which I was very proud). Then, at the end of the presentation, one of my students ([Russ O’Brien](https://www.linkedin.com/in/russellobrien)), raised the hand saying: “It looks like the formula I developed is more efficient than yours, do you mind looking at it?”.

This always happens, Luke pretending to be better than Yoda… with a fatherly smile on my face I projected the formula on the screen, analyzed performances and… WOW! The fatherly smile quickly faded away replaced by total admiration for the code. The approach of Russ is amazingly simple in the idea and stunning fast. His original formula worked only with new customers in a single date, so I had to slightly modify the code to make it work with any selection in the filter context, but the original idea is the very same (*please note in this first version we do not use the ADDCOLUMNS/SUMMARIZE pattern, which will be used later in a different version of this measure*):

```dax


DEFINE

    MEASURE Sales[NewCustomers] =

        COUNTROWS (

            FILTER (

                SUMMARIZE (

                    CALCULATETABLE ( Sales, ALL ( 'Date' ) ),

                    Sales[CustomerKey],

                    "DateOfFirstBuy", MIN ( Sales[OrderDate] )

                ),

                CONTAINS ( 

                    VALUES ( 'Date'[FullDate] ), 

                    'Date'[FullDate], 

                    [DateOfFirstBuy] 

                )

            )

        )

EVALUATE

FILTER (

    ADDCOLUMNS ( 

        VALUES ( 'Date'[FullDate] ), 

        "CountOfNewCustomers", [NewCustomers] 

    ),

    [CountOfNewCustomers] > 0

)

ORDER BY 'Date'[FullDate]

```

This code runs in 40 milliseconds on the Contoso database I use for tests, with 18K customers and 12M rows in the Sales table. On the same database, my best code was running in around 2 seconds. Said in other words, this formula is 50 times faster than my previous best choice, which I post here as a reference:

```dax


MEASURE Customer[NewCustomersSet] =

    VAR CurrentCustomers = VALUES ( Sales[CustomerKey] )

    VAR OldCustomers = 

        FILTER (

            CurrentCustomers,

            CALCULATE ( 

                MIN ( Sales[OrderDate] ), 

                ALL ( 'Date' ) 

            ) < MIN ( 'Date'[FullDate] )

        )

    RETURN COUNTROWS ( EXCEPT ( CurrentCustomers, OldCustomers ) )

```

What is so remarkable about Russ’ code? The idea. All of my formulas focused on searching, customer by customer, if there are no sales before the current date and there are some in the current period. The set version looks elegant mainly because it uses the new set functions in DAX, but it is still slow. On the other hand, Russ went for a totally different way of computing the value:

1. First, it scans the fact table computing, for each customer, his first date of sale
2. Then, it checks which customers have a minimum sale that falls in the current period

The first step, scanning the fact table, results in a single VertiPaq query that is executed only once and results in pure Storage Engine. Once cached, it is not computed anymore. The second step results in Formula Engine usage, but the cardinality of the table to scan is so reduced that the time spent in Formula Engine is really tiny.

I played a bit more with the code, searching for further optimizations, and the best I could do was to replace [[SUMMARIZE]] with a slightly faster [[ADDCOLUMNS]], reducing the execution time to 32 milliseconds, barely measurable as an improvement:

```dax


MEASURE Sales[NewCustomers] =

    COUNTROWS (

        FILTER (

            CALCULATETABLE (

                ADDCOLUMNS (

                    VALUES ( Customer[CustomerKey] ),

                    "DateOfFirstBuy", CALCULATE ( MIN ( Sales[OrderDate] ) )

                ),

                ALL ( 'Date' )

            ),

            CONTAINS ( 

                VALUES ( 'Date'[FullDate] ), 

                'Date'[FullDate], 

                [DateOfFirstBuy] 

            )

        )

    )

```

My suggestion to you is to spend some time looking at the query plan of the formula with DAX Studio and give this formula a try with your data model, my guess is that you will love the approach, which can be extended to compute returning customers, sales of new and returning customers and, with some more work, compute customer retention.

Have fun with DAX, and a big thank to Russ for showing that the power of new ideas, with the power of DAX, really shines!

[[SUMMARIZE]]

Creates a summary of the input table grouped by the specified columns.

`SUMMARIZE ( <Table> [, <GroupBy_ColumnName> [, [<Name>] [, [<Expression>] [, <GroupBy_ColumnName> [, [<Name>] [, [<Expression>] [, … ] ] ] ] ] ] ] )`

[[ADDCOLUMNS]]

Returns a table with new columns specified by the DAX expressions.

`ADDCOLUMNS ( <Table>, <Name>, <Expression> [, <Name>, <Expression> [, … ] ] )`
