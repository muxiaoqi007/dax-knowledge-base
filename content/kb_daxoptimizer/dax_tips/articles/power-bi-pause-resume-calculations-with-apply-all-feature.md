---
title: "Power BI – Pause/Resume calculations with Apply All feature"
url: "https://dax.tips/2021/05/03/power-bi-pause-resume-calculations-with-apply-all-feature"
published: "2021-05-03"
updated: "2021-05-03"
source: "dax.tips"
---

Power BI doesn’t yet have a feature that allows end-users to turn on/off the ability to process calculations for visuals on a report page until they are ready. Most of the time, this is perfectly fine – however, in some instances, it can be handy to disable long-running and heavy calculations from running. At the same time filters/slicers are get selected.

The scenario you most likely want to have this control is when your model uses Direct Query mode against large tables in data sources that charge you for query processing. Even if your Direct Query data source does not charge per query, having a user make quick-fire selections over several slicers can potentially saturate a back-end data-source and unnecessarily chew up resources.

In this article, I show how you can use [calculation groups](https://docs.microsoft.com/en-us/analysis-services/tabular-models/calculation-groups?view=asallproducts-allversions) to provide end-users with a way to turn on/off calculations in visuals, so they only run when they are ready.

Because this technique users calculation groups, it only works for [explicit measures](https://docs.microsoft.com/en-us/analysis-services/tabular-models/calculation-groups?view=asallproducts-allversions#precedence) – which you should be using anyway 😉

## Quickfire summary

For those used to working with calculation groups, the super-fast summary to this is as follows. I will include a detailed step by step explanation further down along with a video example.

1. Create a new calculation group with two calculation items
2. The first calculation item should be called APPLY, and the DAX for this is just SELECTEDMEASURE()
3. The second calculation item should be called FREEZE, and the DAX for this is just BLANK()
4. Give your calculation group a high precedence
5. Add the calculation group as a Filter on all pages in Power BI Desktop
6. Check the Require single selection option

When the user selects the FREEZE option in the filter on all pages, the DAX expression for every explicit measure on the report page will be overwritten. The BLANK() function stops each measure dead in its tracks.

Only when the user selects the APPLY option in the filter panel, do all the explicit measures evaluate their original expression.

## Detailed setup (with screenshots)

The first step to add this functionality to your report is to add a calculation group to your Power BI report. The easiest way to do this today is via [Tabular Editor](https://tabulareditor.com/), a free, community-built tool with lots of great features to help enhance your models.

### 1 – Launch Tabular Editor

If you have Tabular Editor installed, you can launch it from the External Tools.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B1.jpg?resize=746%2C148&ssl=1)

### 2 – Add calculation group

Once you are in Tabular Editor, add a New Calculation Group from the Model menu.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B2.jpg?resize=492%2C394&ssl=1)

Give your new calculation group a meaningful name. I called mine **Apply All**.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B3.jpg?resize=619%2C451&ssl=1)

### 3 – Add calculation group item

Add a calculation item by right-clicking the Apply All calculation group to select the Create New -> Calculation Item from the context menu.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B4.jpg?resize=610%2C494&ssl=1)

Give the first calculation item a meaningful name, such as Apply and set the DAX expression for this to be just the [SELECTEDMEASURE()](https://docs.microsoft.com/en-us/dax/selectedmeasure-function-dax) function.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B5.jpg?resize=800%2C391&ssl=1)

### 4 – Add second calculation group item

Then add a second calculation item called Freeze that uses the BLANK() function in the DAX expression.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B6.jpg?resize=790%2C377&ssl=1)

Finally, if you plan to use more calculation groups in this report, make sure this one has a higher precedence setting than any other calculation group in the model.

Click the save option in the File menu to apply the Power BI Desktop PBIX file updates.

### 5 – Add calculation group to report

Back in Power BI Desktop, you should see the new calculation group appear as a table in the field list.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B7.jpg?resize=681%2C596&ssl=1)

Drag the **Name** field from the **Apply All** calculation group to the **Filters on all pages** section of the **Filter Panel.** Be sure to check the **Require single selection** option.

From this point, any time the **Freeze** filter is applied, all explicit measures will effectively disabled. This filter allows users to adjust slicer and filter settings or make adjustments to visuals quickly without sending back-end queries.

When users have finished making adjustments, they need to select the Apply filter to resume regular operation.

## Video example

## Summary

I would consider this very much a hack/workaround to help improve the user experience. This technique is helpful until we can pause/resume calculations via a button somewhere in the GUI.

Remember, this only works for explicit measures, so implicit measures will continue to evaluate regardless of the calculation group setting.

You can also add the calculation group to the canvas if you prefer it to be front and center.

I think this also shows how powerful calculation groups can be to overwrite/enhance existing measures.

As always, please let me know what you think in the comments. I read them all even if I don’t get the chance to reply in detail right away.

5
10
votes

Article Rating
