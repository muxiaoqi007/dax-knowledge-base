---
title: "Power BI – Dynamic Axis"
url: "https://dax.tips/2020/02/17/power-bi-dynamic-axis"
published: "2020-02-17"
updated: "2020-02-17"
source: "dax.tips"
---

A question I come across in Power BI from time to time is how to configure a report to show an end-user a list of options, and then have visuals in the report use the selected item on the axis. There are a few ways this can get solved, such as using bookmarks. For this blog, I want to share a data-modelling based solution to this challenge.

The PBIX file for this blog can get [downloaded here](https://1drv.ms/u/s!AtDlC2rep7a-y1KIKrMVFjMwUfKB?e=lCXuJP)

Dynamic Axis example

The example video above shows a user selecting options like **Day**, **Month** or **Year** from a slicer, and the three other visuals on the page react to show the selected item as the grain for the axis.

When **Month** is selected, the grid, line and column visuals all show data aggregated to calendar month. Likewise, when the user selects **Year**, all the visuals now show data aggregated to the calendar year.

An additional requirement I tackle in this blog is to control the number of periods shown in a visual.

### The data modelling approach

The first step is to create a table in the model, which will get used to control the dynamic groupings. This table will include the column used by the slicer. In my example, the control table is called Period, and this table contains batches of rows that are grouped with a type of Year, Month, Week or Day.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/02/B1-1.png?resize=688%2C758&ssl=1)

Data for the Period table can get generated externally of Power BI, but here is a DAX based version to create the table. The following DAX code uses the UNION function to align four blocks of date types vertically. Each block repeats every row from the **Dimension Date** table, but stores the appropriate for the block in the **Period** column.

```
Period = 
UNION
    (
        SELECTCOLUMNS(
            'Dimension Date' ,
            "Date" , [Date] ,
            "Period" , [YearID] ,
            "Order By" , [Calendar Year] ,
            "Type" , "Year" ,
            "TypeID" , 4
        ),
        SELECTCOLUMNS(
            'Dimension Date' ,
            "Date" , [Date] ,
            "Period" , [MonthID] ,
            "Order By" , [MonthID] ,
            "Type" , "Month",
            "TypeID" , 3
        ),
        SELECTCOLUMNS(
            'Dimension Date' ,
            "Date" , [Date] ,
            "Period" , [Date] - (WEEKDAY([Date],1)-1),
            "Order By" , [Date] ,
            "Type" , "Week",
            "TypeID" , 2
        ),
        SELECTCOLUMNS(
            'Dimension Date' ,
            "Date" , [Date] ,
            "Period" , [Date] ,
            "Order By" , [Date] ,
            "Type" , "Day",
            "TypeID" , 1
        )
    )
```

It’s important to note here that a specific date will appear in the final table multiple times. In this case, each date from Dimension Date will appear four times, so a traditional 1:many relationship cannot be used to connect this table to the rest of the model. This approach is perfectly fine!

The TypeID column can be used to sort the Type column to control the order the text gets displayed in the slider.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/02/B5-1.png?resize=793%2C577&ssl=1)

The sample set of data from the Period table will look something like this.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/02/B4-1.png?resize=642%2C393&ssl=1)

The details for the relationship used are shown in the image below. The cardinality of the relationship gets set to **many to many (\*:\*)** and the Cross filter directly is set to **Single (Period filters Dimension Date)**. Ignore the warning about Many to Many.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/02/B3-1.png?fit=1000%2C967&ssl=1)

The **Type** column from the **Period** table can now be used in a slicer to present the end-user with options to filter. The slicer can be filtered to be Year/Month/Day, or Year/Week/Day etc.

The **Period** column in the **Period** table can now get used in visuals. For most visuals, this column can be used as the Axis, while for a Table visual just add the column to the Values area, and rows or columns for the Matrix.

### Fixing Relative Periods

Existing measures should now just work with visuals configured with the dynamic period – however, you may find you get too many rows/columns when selecting Days or Months. A bonus item for this blog shows how you can enhance your existing measures to show the relative *N* periods for better formatting.

The first step is to create a helper measure called Dynamic Relative Date, which determines the exact date back in time that should get used as the lower boundary. My example uses the following calculated measure.

```dax
Dynamic Relative Date = 
VAR Blocks = 10
VAR RelativeBlocks = Blocks -1
VAR SelectedDateType = SELECTEDVALUE('Period'[Type])
VAR myLatestDate = 
    CALCULATE(
        LASTDATE('Fact Sale'[Invoice Date Key]),
        ALL()
        )

VAR baseYear = 
    YEAR(myLatestDate) + 
    IF(SelectedDateType="Year",0-RelativeBlocks,0)

VAR baseMonth = 
    MONTH(myLatestDate) + 
    IF(SelectedDateType="Month",0-RelativeBlocks,0)

VAR baseDay = 
    DAY(myLatestDate) + 
    SWITCH(
        SelectedDateType ,
        "Day" ,  0-RelativeBlocks,
        "Week" , (0-RelativeBlocks) * 7 ,
        -DAY(myLatestDate)+1 
        )

RETURN 
    DATE(baseYear,baseMonth,baseDay)

```

The **Blocks** variable at line 2 determines how many relative buckets get shown each time. The variable could get improved by combining with a What-If slicer to provide extra functionality for end-users.

With this measure in your model, the following filter can be added to your calculations to restrict how far back in time each calculation will go. In some cases, this may speed up your reports. 🙂

```dax
Sales = 
    CALCULATE(
        SUM('Fact Sale'[Total Including Tax]),
        FILTER('Dimension Date',[Date]>=[Dynamic Relative Date] )
    )

```

and

```dax
Rows = 
    CALCULATE(
        COUNTROWS('Fact Sale'),
        FILTER('Dimension Date',[Date]>=[Dynamic Relative Date] )
    )

```

The FILTER function in each CALCULATE statement passes an instruction to only use dates after the value calculated in the **[Dynamic Relative Date]** measure.

And there you go, a data modelling based solution for Power BI that doesn’t involve bookmarks.

As always, please let me know what you think, especially if you end up implementing this or something similar.

A big thank you to Chris Hamill for the professional layout. You can learn a tonne from him over at his blog at <https://alluringbi.com/>

5
4
votes

Article Rating
