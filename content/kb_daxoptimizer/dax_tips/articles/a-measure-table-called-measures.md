---
title: "A measure table called “Measures”"
url: "https://dax.tips/2019/05/14/a-measure-table-called-measures"
published: "2019-05-14"
updated: "2019-07-07"
source: "dax.tips"
---

I wrote a [blog](https://radacad.com/how-to-better-organise-your-power-bi-measures) back in 2017 on how to better organise your measures in Power BI. One thing that bothered me at the time was I wasn’t able to give the measure table the one name I truly wanted.

The perfect name for a measure table is, of course, **Measures**. Unfortunately, this is a reserved word and gets blocked, so I always ended up calling my measure table something like **All Measures**, **My Measures** or **!Measures**.

These table names work OK, but I wanted to use the name **Measures**. Well, today, like all useful discoveries, I accidentally stumbled on a method that allows me to name the table the way I originally wanted.

The trick is to place square parenthesis around the name of the table, but include a space before the table name, as follows:

`[ Measures] = {BLANK()}`

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/05/2019-05-14_19-24-55-1.jpg?resize=596%2C406&ssl=1)

Adding a measure table called Measures

In Power BI Desktop, use the **New Table** option on the Modeling tab and post the above code. This action will create a new dummy table with a single column/single row. This column can get hidden, so the only objects that appear against the **Measures** table are calculated measures.

Note the tidy syntax to create a minimalist table using the {} brackets. This approach is a syntax I find myself using more and more in tools like DAX Studio when running quickfire queries against a model, as an efficient way of generating the table output required.

`EVALUATE {COUNTROWS('Sales')}`

The bonus of using the space character as a prefix is it places the new table at the top of the field list. I did wonder if this approach might mean the table name in the field list would no longer be vertically left-aligned with the other table-names, but luckily the Power BI Desktop UI strips the leading space out to appease those with severe cases of OCD.

Once you have this table in your model, you can use it to host all your measures, including using sub-folders. The fully qualified name for these measures *will* include the space, e.g. **‘ Measures'[Sum of Sales]**, but given the number #1 crime in DAX is to add the table-name prefix when referencing measures in code, this isn’t going to be a problem. Right? 🙂

5
5
votes

Article Rating
