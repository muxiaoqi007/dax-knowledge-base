---
title: "Row-based Time Intelligence"
url: "https://dax.tips/2019/10/09/row-based-time-intelligence"
published: "2019-10-09"
updated: "2019-10-19"
source: "dax.tips"
---

For this blog, I want to share an interesting data modelling technique which you might consider when having performance problems with time intelligence calculations. This particular pattern works well on very large data-sets, especially for visuals that contain multiple measures and multiple periods.

The idea is to create a Time Intelligence table which contains blocks of rows per period. Some example time-periods might be:

- Current Year
- Prior Year
- Last 2 Months
- Last 3 Weeks
- Last 28 Days
- Year to Date

[Here is a link](https://1drv.ms/u/s!AtDlC2rep7a-xl3tC13gYWpjl4Lx?e=Jm59D8) to a simple PBIX file that demonstrates the technique.

You can have as many periods in the table as you like and the data in this table differs from a traditional calendar table. A traditional calendar table should only contain 1 row per day, have no gaps and only enough rows to cover the time range of your core data-set.

Where a single date can appear multiple times in a row-based Time Intelligence table. Once for every period included in the table.

A row-based Time Intelligence table doesn’t replace the traditional calendar table; rather, it complements it. For specific scenarios, this approach will produce the most optimal query plan in both Import or Direct Query mode.

In advanced scenarios, you may consider having multiple Time Intelligence tables. One may have periods that naturally align to calendar weeks, while another may hold periods that align nicely to calendar months. This type of scenario comes in to play more when you want to take advantage of Aggregation Awareness.

### What makes row-based Time Intelligence fast?

DAX calculations that use a row-based Time Intelligence element have a much better chance of taking advantage of [[dax-fusion|DAX Fusion]]. This optimization attempts to combine similar tasks into a single storage engine query. This is why queries using this technique often don’t increase much in terms of overall time when you add additional time periods.

### How does it work?

The basic version of a row-based Time Intelligence table only has three columns, and a simple version of the pattern might look as follows:

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B1.png?resize=754%2C879&ssl=1)

The ‘Time Intelligence’ table can be related to a Calendar table. In this scenario, the relationship should be BiDirectional so that selections made to a field in the Time Intelligence table propagate through to your Fact table. In this case the Fact table is ‘Fact Sale’.

Typically, you should be cautious when configuring a relationship in your model to be Bi-directional – but in this case, it is okay. The [other approach](#other) is to connect the ‘Time Intelligence’ table directly to your FACT table using a direct many-to-many relationship with the cross filter set in a single direction. There is little difference between either approach in terms of performance, or query plan (more about this below).

The data for the Time Intelligence table can be generated in DAX or your data-source. If you generate it in DAX, you can’t take full advantage of the table in composite models.

Here are some links with examples on how to generate a Time Intelligence table in either a SQL DB, or as a calculated table directly in your model.

- [TSQL Script](https://1drv.ms/u/s!AtDlC2rep7a-xkO6Cl4VMr0nzbv4?e=SQHDpc)
- [DAX Script](https://1drv.ms/u/s!AtDlC2rep7a-xk6KSVmoffED7p0v?e=drnK0N)

The sample scripts only include patterns to generate about 6 periods but should give you the idea on how to easily add as many additional periods needed to suit your organisations reporting requirements.

The basic three-column Time Intelligence table should look something like this:

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B4.png?resize=562%2C486&ssl=1)

For simple periods, the value in the [Axis Date] column have a 1:1 relationship with the value in the [Date] column. However, for cumulative periods, there are multiple [Date] rows per [Axis Date] value.

The following image shows a version of this table filtered, so the [Period] is “Totals YTD” and the [Axis Date] is 4th of January, 2016. Note there are 4 rows and not a single row. When the [Axis Date] gets filtered to the 5th of January, there should be 5 rows.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B5.png?resize=562%2C179&ssl=1)

### Advantages

One advantage of this data modelling approach is for visuals that show a mixture of multiple periods combined with multiple measures. The visual below shows 5 different measures in the rows, while 6 different time periods are showing in the columns.

![](https://i1.wp.com/dax.tips/wp-content/uploads/2019/10/B2.png?fit=1000%2C346&ssl=1)

Another variation on this visual is a matrix showing a period comparison index measures (this year vs last year) for a range of periods. This case can be optimised further by pivoting data from rows to columns during the ETL process, which I plan to cover in an article soon.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B3.png?resize=812%2C228&ssl=1)

Not all visuals in the report need to use fields from the row-based Time Intelligence table. Some visuals can use fields from the traditional calendar, while the more complex visuals can use this.

In both examples above, the [Date] field from the Time Intelligence table gets used in relationships. Relationships from the TI table can either be to a Calendar table (BiDi) or a Fact table (Direct M2M). The other two fields in the TI tables ([Period] and [Axis Date]) are the fields you would add to your visual.

Performance testing on very large data-sets shows this technique can be at least twice as fast in complex visuals like these examples, that use a mixture of periods and measures.

This approach also allows you to easily offer dynamic periods when combined with a slicer showing the various periods. An end-user can dynamically control which columns (or rows) are visible based on a slicer selection.

### Disadvantages

An additional table with periods could be confusing for an end-user. Ideally, the report author who designs a report using this table does so in a way that reduces any confusion. Use fields from the traditional calendar table for visuals where it makes sense and only uses fields from the row-based Time Intelligence table for more complex.

The row-based Time Intelligence table does not perform point-in-time well. So, if you were interested in seeing how everything was from a specific point in the past, you may get some strange results. This disadvantage can probably get solved through additional columns in the row-based Time Intelligence table which I will cover in a future article.

This approach can be interesting getting to work with Aggregate Awareness. However, I have an article planned to describe how you can configure a model to row-based Time Intelligence tables with Agg. Awareness.

### How fast is it?

Consider the following DAX query over a Direct Query mode to an Azure SQL DW running at 400DTU.

```dax
DEFINE 
MEASURE 'fact MyEvents'[Events] = SUM('fact MyEvents'[Quantity_ThisYear])
MEASURE 'fact MyEvents'[Events LY] = SUM('fact MyEvents'[Quantity_LastYear])
MEASURE 'fact MyEvents'[Events Diff] = [Events] - [Events LY]
MEASURE 'fact MyEvents'[Events Diff %] = 
	VAR ty = [Events]
	VAR ly = [Events LY]
	RETURN (ty-ly) / ty
EVALUATE
SUMMARIZECOLUMNS(
      'dim TimeIntelligence'[Period],
      "Events", 	'fact MyEvents'[Events],
      "Events_LY", 	'fact MyEvents'[Events LY],
      "Events_Diff", 	'fact MyEvents'[Events Diff],
      "Events_Diff__", 	'fact MyEvents'[Events Diff %]
    )

```

The **‘fact MyEvents’** table in this query, has **8.2 Billion rows** and contains telemetry data covering 2 years to September 2019. The core [Events] metric is a simple SUM calculation over a column in the 8.2B row table. This example uses a pretty heavy query. There are no aggregations, or summary tables in play for this example.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B7.png?resize=534%2C399&ssl=1)

For each row in the result, the engine needs to calculate a value using the appropriate measure for every column. In this case, **the query took 29 seconds to complete**. As you see from the captured server timings, only three storage engine tasks took place. Of the three SE Queries, only a single T-SQL Direct Query statement gets sent to the back-end source. The image below shows the SE Query that consumed most of the 29 seconds.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B8.png?resize=612%2C234&ssl=1)

By comparison, the server timings for a [measure based approach](https://1drv.ms/u/s!AtDlC2rep7a-xlcpf3kObybtW9L9?e=jBwmXt) to generate the same values is shown below. **The measure based query takes 87 seconds** compared with 29 seconds on the same hardware. The lack of [[dax-fusion|DAX fusion]] means more storage engine queries take place to generate the same result.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B9.png?resize=732%2C271&ssl=1)


|  |  |
| --- | --- |
| Row-based TI | 29 seconds |
| Measure Based TI | 87 seconds |

### The other approach

The [other approach](https://1drv.ms/u/s!AtDlC2rep7a-xkrHOk1bxfQquD3q?e=6nwVwY) is to use a direct many-to-many relationship between the row-based Time Intelligence table and the fact table.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B10.png?resize=789%2C844&ssl=1)

The specifics of the relationship are shown below. The **Cardinality** for this relationship (1) should be set to Many to Many, while the **Cross filter direction** (2) should be one way.

![](https://i1.wp.com/dax.tips/wp-content/uploads/2019/10/B11.png?fit=1000%2C967&ssl=1)

Out of interest, the same DAX query used to in the example over the 8.2 billion row table produced the following plan. The overall query time was faster at 25 seconds, compared with 29 seconds and there is 1 less storage engine query event – which reflects a reduced number of relationships involved in the design.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/B12.png?resize=566%2C237&ssl=1)

### Summary

I’m not suggesting you use this approach for all models. Just consider the technique, especially if you are working with larger data-sets and you aren’t happy with the performance of your existing time intelligence calculations.

I have applied this design a few times this year on some very large models and have found it to provide the best performance, when it comes to Time Intelligence calculations.

As always, capture a baseline and use load testing to really flesh out how the model will perform with high concurrency.

5
10
votes

Article Rating
