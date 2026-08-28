---
title: "Overriding slicers"
url: "https://dax.tips/2020/02/05/overriding-slicers"
published: "2020-02-04"
updated: "2020-02-04"
source: "dax.tips"
---

A question I’ve received a couple of times recently is how to override slicer selections in Power BI visuals. Or more specifically, if the user selects a specific item in a slicer (or filter), how can I show data for other items a visual that may be related by my selection.

An example of this is a report showing a date slicer, where a user can select a specific date, but we want to display data for a range of dates before the selected date. This challenge is not the same as using a measure showing a single value that represents a range of dates; rather, we want to show more rows than what gets selected in the slicer.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/02/B1.png?resize=772%2C279&ssl=1)

In the image above, the **29th of December** is selected in the slicer, and the table visual is showing more than just data for this specific date. In this case, the [Revenue (Dynamic)] measure is hard-coded to show values for the five days preceding the selected date.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/02/B2.png?resize=742%2C270&ssl=1)

The update image shows the **27th December** selected and the table show shows a set of dates related to the slicer selection.

### How to solve

The trick to getting this working is to create a copy of the column used in the slicer in another table. Then, use the cloned column in both the axis of your visual AND in the DAX for your calculated column.

The first step is to clone the column from the slicer. In my case, I used the following DAX to create a single-column calculated table.

```
Dummy Date Table = ALL('Date'[Date])
```

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/02/B3.png?resize=547%2C310&ssl=1)

I then create a relationship from the cloned table to the FACT table as follows.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/02/B4.png?resize=776%2C518&ssl=1)

Once this is in place, I can use the [Date] column from the ‘Dummy Date Table’ on the axis of my visual. Initially, this will produce a row for every value in the column regardless of what selections are set in the slicer.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/02/B5.png?resize=813%2C362&ssl=1)

The final part of the puzzle is to create a measure that checks the selected value from the slicer, and only returns a value for dates from the ‘Dummy Date Table’ that fall inside a prescribed range.

```dax
Revenue (Dynamic) = 
VAR mySlicerDate = SELECTEDVALUE('Date'[Date])

RETURN 
    CALCULATE(
        SUM('Sales'[Revenue]),
        ALL('Date'),
        FILTER(
            'Dummy Date Table',
            'Dummy Date Table'[Date] >  mySlicerDate - 5 && 
            'Dummy Date Table'[Date] <= mySlicerDate
            )
        )

```

Once a measure gets added to a visual, the default behaviour is the axis will only show a row when there is a non-blank value. The DAX code from this measure produces a blank value for every date outside the hard-coded five-day range. This default behaviour can get overridden, but we don’t want to do this in our case.

If more measures need to be added to the visual, the same DAX pattern should apply to each measure.

The combination of the cloned column and dynamic measure works well in all kinds of visuals such as the bar chart shown in the video below.

In my case, I also added a “What-If” parameter allowing the user to select a date range using a slider dynamically.

This technique can apply to scenarios other than dates. I used a date column here for this example, but by changing the DAX in the measure – you can use filters to find and show other rows of data that have a relationship to the value selected in the slicer.

The PBIX used in this example can be [downloaded here](https://1drv.ms/u/s!AtDlC2rep7a-y0hUW21YJnICQaeR?e=WAOT4C).

As always, I’d love to hear from you if you find this technique useful and successfully apply it in your models.

4.9
8
votes

Article Rating
