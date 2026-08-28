---
title: "Dynamic Columns based on values"
url: "https://dax.tips/2020/01/28/dynamic-columns-based-on-values"
published: "2020-01-27"
updated: "2020-01-28"
source: "dax.tips"
---

I came across an interesting request last week from someone who wanted to dynamically control columns shown in a Power BI table visual – based on the values within each table.

There are lots of blogs showing different techniques on dynamic columns, but the requirement here was to base the decision on whether to show or hide the column, using logic that prefers columns populated with the most cells.

The report needs to show a set of KPI data in a tabular form (table or matrix visual). Each column represents a different KPI and each KPI will have its own calculated measure. There are approximately 50 KPI’s in the model, and the objective is to find a way to remove the need to scroll horizontally to find the columns that have the most data.

The number of columns shown needs to be interactive with the end-user having the ability to show more (or all) of the columns if they wanted.

This challenge can get solved in several ways, but I thought I would see what a DAX-based solution would look.

The working PBIX file for this blog can be [downloaded here](https://1drv.ms/u/s!AtDlC2rep7a-yzdrVdRweDqetNqA?e=AewrZJ).

The starting point is a table visual in the report page called “The Problem”, that contains 11 columns of measures.

![](https://i2.wp.com/dax.tips/wp-content/uploads/2020/01/B1-1.png?fit=1000%2C528&ssl=1)

Each column is based on a different measure, and will return a value depending on the product from the left-hand column.

Imagine there are 50 of these columns and depending on the filter settings, some columns may return lots of values, while other columns may only display a few (or even none).

In my example, I created 11 measures that return a mixture of values. The contents of these measures could get replaced with your logic.

The DAX-based solution gets shown in the report tab named “The Solution”.

The report shown in the video has a matrix of columns. Each column represents a specific KPI and is based on a measure. The slicer just above the matrix allows a user to select a range of numbers that control which columns get shown. If the chosen range is from 1 to 4, then the first four columns with the most number of rows will be displayed.

If a user sets the slicer to use the entire range, then all columns will be displayed, The user can also set the slicer to show the least populated columns buy setting a range starting near the end.

### The mechanics

To get this working, first I created a disconnected table with 1 row per measure. I have 11 measures, so my disconnected table has 11 rows. I used the following code that created three columns.

```
Dynamic Measures = 

DATATABLE(
    "Measure ID" , INTEGER ,
    "Measure Name" , STRING ,
    "Format" , STRING ,
        { 
            { 1,"Revenue","CURRENCY"} ,
            { 2,"Measure A","#,###"} ,
            { 3,"Measure B","#,###"} ,
            { 4,"Measure C","#,###"} ,
            { 5,"Measure D","#,###"} ,
            { 6,"Measure E","#,###"} ,
            { 7,"Measure F","CURRENCY"} ,
            { 8,"Measure G","CURRENCY"} ,
            { 9,"Measure H","CURRENCY"} ,
            {10,"Measure I","CURRENCY"} ,
            {11,"Measure J","CURRENCY"} 
        }
    )
```

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/01/B2.png?resize=412%2C390&ssl=1)

Table of measures for dynamic column selection

With this table in place, the [Measure Name] column from this table can get used on the matrix visual. This configuration provides the structure for showing/hiding columns dynamically.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/01/B3-1.png?resize=289%2C717&ssl=1)

The next step is to create a calculated measure to be used in the matrix. This measure will make calls to other measures to produce the values shown in the matrix.

```dax
Dynamic Measure = 
VAR myMeasureName = MIN('Dynamic Measures'[Measure Name])
VAR myProduct = MIN('Product'[Product])

VAR myWorkingTable = 
 FILTER(
        ADDCOLUMNS(
            GENERATE(
                ALLSELECTED('Product'[Product],'Product'[Segment]) ,
                ALL('Dynamic Measures'[Measure Name],'Dynamic Measures'[Format])
            ) ,
        "Measure Value" ,
        SWITCH(
            [Measure Name],
            "Measure A" , [Measure A] ,
            "Measure B" , [Measure B] ,
            "Measure C" , [Measure C] ,
            "Measure D" , [Measure D] ,
            "Measure E" , [Measure E] ,
            "Measure F" , [Measure F] ,
            "Measure G" , [Measure G] ,
            "Measure H" , [Measure H] ,
            "Measure I" , [Measure I] ,
            "Measure J" , [Measure J] ,
            [Sales]
            ) 
        ),NOT ISBLANK([Measure Value]))

VAR myMeasureTableToRank =

SUMMARIZE(
	myWorkingTable,
	[measure name],
	"Count of Non Blank Values",
	VAR t = [Measure Name] RETURN COUNTROWS(filter(myWorkingTable,[Measure Name] = t)))


VAR myMeasureTableRanked = 
    ADDCOLUMNS(
        myMeasureTableToRank ,
        "Rank" , RANKX(myMeasureTableToRank,[Count of Non Blank Values],,,Dense)
          
        )

VAR myCellValue =
    MAXX(FILTER(myWorkingTable,[Measure Name] = MyMeasureName && [Product] =myProduct),[Measure Value])

VAR myCellFormat = 
    MAXX(FILTER(myWorkingTable,[Measure Name] = MyMeasureName && [Product] =myProduct),[format])

VAR myRank = 
    MAXX(FILTER(myMeasureTableRanked,[Measure Name] = myMeasureName),[Rank])    

VAR myFinalValue =
    IF(
        NOT ISBLANK(myCellValue),
        FORMAT(myCellValue,myCellFormat)
        )

RETURN 
    IF(
         myRank IN FILTERS('Column Filter'[Value]) ,
         myFinalValue
    )

```

The logic of the measure does the following:

- Creates a working at line 5 that computes all values for all measures. This table gets assigned to the variable called ***myWorkingTable***. You need to manually add a line to the SWITCH statement for every measure considered. **You** need to ensure this is in sync with the rows in the physical table called ‘Dynamic Measures’
- Creates a new table that counts the number of non-blank rows per column. This result is grouped and stored in a variable called ***myMeasureTableToRank***
- Adds a ranking column to the table stored in the ***myMeasureTableToRank*** variable. This new table is stored in the variable called ***myMeasureTableRanked***
- The ***myCellValue*** variable is assigned the appropriate value for the current cell from the ***myWorkingTable*** table.
- The ***myCellFormat*** variable is assigned the appropriate format string for the value. This is pretty cool!
- The ***myRank*** variable is assigned the appropriate rank value for the column
- The rest of the measure decides if the **myRank** value sits within the current range specified by the slicer. If it does, the value is shown otherwise the measure will return a blank.

This measure gets added to our matrix as the only measure. The measure will only return a value if the ranking of the column happens to sit within the range determined by the slicer.

The default behavior of a matrix is if column is full of blanks it will not be shown. This can be overridden in the formatting properties – but we don’t want that in this case.

I set the alignment formatting for the matrix to be right aligned. Otherwise the code allows for values to be shown using a variety of formatting strings by populating the measure table appropriately.

![](https://i2.wp.com/dax.tips/wp-content/uploads/2020/01/B4.png?fit=265%2C1024&ssl=1)

The last element was to add a slicer to the report that allows users to set a range of values to control which columns are shown.

An ordering column can be added to the ‘Dynamic Measures’ table if column order is important.

### Summary

This solution seemed to work pretty well and could be useful for specific use cases. It was pretty straight forward to implement and included some neat tricks such as :

- Pre-calculating a table of values in a measure
- Adding a ranking column on the fly to a virtual table
- A nice way to add a format string on the fly for different types of output

This small example performs pretty well – but care would need to be taken if the logic in the measures becomes complex. DAX would need to be super-optimised when working with larger data-sets, more columns and more complex logic.

Different rules can be applied to decide the prioritisation of column order – but the structure of the approach should pretty much stay the same.

Let me know if you use this. I’m always interested to hear when ideas shown in my blogs are applied,

4.9
15
votes

Article Rating
