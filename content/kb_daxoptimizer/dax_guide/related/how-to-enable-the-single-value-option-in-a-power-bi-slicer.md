---
title: "How to enable the Single Value option in a Power BI slicer"
url: "https://www.sqlbi.com/articles/how-to-enable-the-single-value-option-in-a-power-bi-slicer"
published: "2021-12-01"
updated: 
source: "sqlbi.com"
---

# How to enable the Single Value option in a Power BI slicer

> 发布：2021-12-01

When creating a New Parameter in Power BI, you get a Slicer with a Single Value option. This option is not available when you use other columns of your model in a slicer.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-20.png)

This option is visible when the column has particular metadata applied to it by Power BI so that you also see a special icon in the Fields pane.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-18.png)

You cannot apply the same behavior to a column of a table you created or imported by using Power BI. However, Tabular Editor is your key to unlocking this feature. The article shows the user interface of the [free version of Tabular Editor](https://github.com/TabularEditor/TabularEditor/releases/latest); the steps required are identical in [the commercial version of Tabular Editor](https://tabulareditor.com/).

**IMPORTANT DISCLAIMER:  The properties modifications suggested in the following description are not supported by Microsoft. You apply these changes at your own risk. You should always create a backup of the Power BI file before modifying it.**

There is an extended property to the *Parameter* column in the *Parameter* table. That extended property is called ParameterMetadata and contains the following string in JSON format:

```dax


{"version":0}

```

We can see this property by opening Tabular Editor from inside the Power BI file.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-14.png)

In the same model, we create a MyData calculated table defined as follows:

```dax


MyData = 

SELECTCOLUMNS ( 

    {0.5,1.5,2,3,7,8.2,11,20,50,100},

    "Number", [Value]

)

```

We find a regular Number column in the new column.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-14.png)

Using Tabular Editor, we select the column we want to enable for the Single Value selection in the slicer and click the ellipsis button in the Extended Properties property of the Metadata section.

![](https://cdn.sqlbi.com/wp-content/uploads/image5-11.png)

In the following dialog box, we click the Add button, assign these properties, and then click OK:

- Name: ParameterMetadata
- Value: {"version":0}

![](https://cdn.sqlbi.com/wp-content/uploads/image6-9.png)

In Tabular Editor, we save the changes to the Power BI model. The parameter icon is now showing next to the *Number* column.

![](https://cdn.sqlbi.com/wp-content/uploads/image7-7.png)

We have now achieved our goal. We can choose the Single Value option in a slicer connected to the *MyData[Number]* column.

![](https://cdn.sqlbi.com/wp-content/uploads/image8-5.png)

The technique shown in this article works strictly on numeric columns. If you are working with a date column, the Single Value option is not available. Changing the column’s data type after adding the ParameterMetadata extended property might negatively impact the behavior of the slicer – use this technique carefully if you do not want to break anything!
