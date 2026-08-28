---
title: "Using calculation groups to switch between dates"
url: "https://www.sqlbi.com/articles/using-calculation-groups-to-switch-between-dates"
published: "2020-12-07"
updated: 
source: "sqlbi.com"
---

# Using calculation groups to switch between dates

> 发布：2020-12-07

It is common to find multiple dates in your *Sales* table. In Contoso, they store the *Order Date* and the *Delivery Date* of each order. One good option to build a model with multiple dates is to create multiple relationships between *Sales* and *Date*. Each relationship is based on one column, and only one of the multiple relationships can be kept active.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-3.png)

When you have multiple relationships in a model, the engine uses the active relationship by default. If needed, you can use [[CALCULATE]] and the [[USERELATIONSHIP]] modifier to change the active relationship. Thus, you can author these two measures to compute the sales amount and the delivered amount:

```dax


Sales Amount := 

SUMX ( 

    Sales, 

    Sales[Quantity] * Sales[Net Price] 

)



Delivery Amount := 

CALCULATE (

    [Sales Amount],

    USERELATIONSHIP ( Sales[Delivery Date], 'Date'[Date] )

)

```

This technique works just fine; it has the disadvantage of creating many measures, one for each combination of relationship to activate and base measure. Another solution is to create a calculation group that changes the active relationship of the selected measure. Doing this, you create one calculation item for each relationship and the user chooses the relationship to activate using a slicer or a report filter.

The result we want to obtain is the following, where the selection of *DateToUse* in the slicer changes the active relationship in the report.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-3.png)

Using Tabular Editor, you can create a calculation group with two calculation items using the following code (the syntax is described [here](https://www.sqlbi.com/calculation-groups/#syntax)):

```dax


DateToUse[DateToUse]."Order Date" =

    SELECTEDMEASURE ()



DateToUse[DateToUse]."Delivery Date" =

    CALCULATE ( 

        SELECTEDMEASURE (),

        USERELATIONSHIP ( Sales[Delivery Date], 'Date'[Date] )

    )

```

This way, all the measures in the report now use the relationship set by the calculation item. If the user does not make any selection, or if they choose *Order Date*, then the default relationship is used. When the user chooses *Delivery Date*, all the calculations are executed with the relationship between *Delivery Date* and *Date* as the active relationship.

Simple calculation groups like this one can make a huge difference in the usability of your reports, greatly reducing the maintenance effort of the model all the while improving the user experience.

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[USERELATIONSHIP]]

CALCULATE modifier

Specifies an existing relationship to be used in the evaluation of a DAX expression. The relationship is defined by naming, as arguments, the two columns that serve as endpoints.

`USERELATIONSHIP ( <ColumnName1>, <ColumnName2> )`

## Articles in the Calculation Groups series

- [Introducing Calculation Groups](https://www.sqlbi.com/articles/introducing-calculation-groups/)
- [Understanding Calculation Groups](https://www.sqlbi.com/articles/understanding-calculation-groups/)
- [Understanding the Application of Calculation Items](https://www.sqlbi.com/articles/understanding-the-application-of-calculation-items/)
- [Understanding Calculation Group Precedence](https://www.sqlbi.com/articles/understanding-calculation-group-precedence/)
- [Controlling Format Strings in Calculation Groups](https://www.sqlbi.com/articles/controlling-format-strings-in-calculation-groups/)
- [Avoiding Pitfalls in Calculation Groups Precedence](https://www.sqlbi.com/articles/avoiding-pitfalls-in-calculation-groups-precedence/)
- [Using calculation groups to selectively replace measures in DAX expressions](https://www.sqlbi.com/articles/using-calculation-groups-to-selectively-replace-measures-in-dax-expressions/)
- *Using calculation groups to switch between dates*
- [Understanding the interactions between composite models and calculation groups](https://www.sqlbi.com/articles/understanding-the-interactions-between-composite-models-and-calculation-groups/)
