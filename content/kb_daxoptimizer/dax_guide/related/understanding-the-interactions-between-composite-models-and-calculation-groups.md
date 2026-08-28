---
title: "Understanding the interactions between composite models and calculation groups"
url: "https://www.sqlbi.com/articles/understanding-the-interactions-between-composite-models-and-calculation-groups"
published: "2022-01-03"
updated: 
source: "sqlbi.com"
---

# Understanding the interactions between composite models and calculation groups

> 发布：2022-01-03

Both composite models and calculation groups are extremely powerful features that were added to Power BI rather recently. Specifically, composite models are changing the way users build their models. Users can extend an existing corporate model rather than starting from scratch every time. That said, when building a composite model you must pay attention to several subtle details: performance, the use of [[ALLSELECTED]], and calculation groups. In this article, we cover only calculation groups, and we take for granted that the reader has some knowledge of local and remote models, and of wholesale and retail queries. If you are not familiar with the terms, you can find more information here: [Introducing wholesale and retail execution in composite models](https://www.sqlbi.com/articles/introducing-wholesale-and-retail-execution-in-composite-models).

Though you can define calculation groups in both the local and the remote models, there is a limitation in the way calculation groups are applied. The limitation is short in its definition, but its effects are rather surprising:

> A local calculation group is applied to any sub-query, whereas a remote calculation group is applied only to wholesale sub-queries; it is not applied to retail sub-queries.

The key to understanding the limitation well is the word sub-query. Any query executed by the local server results in multiple sub-queries. Some are retail (if they use any local table) whereas others are wholesale (if the entire execution can be pushed to the remote server). Both wholesale and retail sub-queries are part of an individual query: part of the query is executed locally and other parts are executed remotely.

In such scenarios, remote calculation groups are applied only to wholesale sub-queries. Any retail sub-query is not affected by the remote calculation group. On the other hand, local calculation groups are applied to both local and remote sub-queries.

The reason is that the DAX engine applies the calculation group before executing the query. The engine must know the definition of the calculation items to apply the calculation group. For a locally-defined calculation group, the engine knows the DAX code used in the definition of the calculation group itself, so that it can apply the calculation group.

The code of remotely-defined calculation groups is not available to the local DAX engine. Therefore, the local DAX engine cannot apply a remotely-defined calculation group to retail code.

In order to better understand the scenario, we prepared a Contoso model with one calculation group containing two items, CY and YTD:

```dax


------------------------------------------------

-- Calculation Group: 'Remote Time Intelligence'

------------------------------------------------

CALCULATIONGROUP  'Remote Time Intelligence'[Remote Time Intelligence] 



    CALCULATIONITEM "CY" = SELECTEDMEASURE ()

    

    CALCULATIONITEM "YTD" = 

        CALCULATE (

            SELECTEDMEASURE (),

            DATESYTD (  'Date'[Date]  )

        )

```

When used in a report, the calculation group works just fine and provides the current value and the year-to-date variation of any measure.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-26.png)

The model is published on the Power BI service. Based on this model, we create a new composite model to simulate a discount based on the product category. Therefore, we add a new table to the composite model containing the discount percentage by category, and then we use the table to compute the discounted price. Below is the new table added to the model.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-24.png)

We create a relationship between *Product* and the new *Discounts* table, based on *Product[Category]*.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-20.png)

The next step is to create a measure that computes the discounted sales. The same measure can be computed in different ways, leading to different execution plans. Instead of providing just one version, we prefer to show different ways to express the code along with a few considerations about the accuracy of the code. Beware that here, we are not interested in analyzing performance. Instead, the focus is only on how different formulations of the same code produce different results.

A first attempt to author the *Discounted Sales* measure could be the following – which is incorrect:

```dax


Discounted Sales (Error) :=

SUMX (

    Sales,

    Sales[Quantity] * Sales[Net Price]

        * ( 1 - RELATED ( Discounts[Discount%] ) )

)

```

This first version does not work, because you cannot use [[RELATED]] to fetch a column through a limited (weak) relationship. [[RELATED]] works only through regular (strong) relationships. You could avoid using [[RELATED]] by replacing it with [[LOOKUPVALUE]], thus mimicking the relationship through more verbose DAX code:

```dax


Discounted Sales (Wrong) :=

SUMX (

    Sales,

    Sales[Quantity] * Sales[Net Price]

        * (

            1

                - LOOKUPVALUE (

                    Discounts[Discount%],

                    Discounts[Category], RELATED ( 'Product'[Category] )

                )

        )

)

```

Apart from performance considerations (this measure is awful), the *Discounted Sales (Wrong)* measure produces the correct result.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-19.png)

Nonetheless, the *Discounted Sales (Wrong)* measure displays a major drawback. Its execution is retail, because in the same expression it is mixing values retrieved by the remote model and values retrieved from the local model. Therefore, most of the calculation is executed locally after the local model retrieved the content of *Sales*. Because the measure is retail, the remote calculation group is not applied to *Discounted Sales*. In the next figure, the YTD calculation item works on *Sales Amount* but it does not produce any effect on *Discounted Sales*.

![](https://cdn.sqlbi.com/wp-content/uploads/image5-16.png)

The measure can be written so that the calculation of *Sales Amount* becomes wholesale before the application of the discount. Indeed, the following version of *Discounted Sales* is faster, easier, and it executes wholesale:

```dax


Discounted Sales :=

SUMX ( 

    Discounts,

    [Sales Amount] * ( 1 - Discounts[Discount%] )

)

```

The optimizer is able to push the calculation of *Sales Amount* to the remote server, keeping only the multiplication in the local engine. As such, the measure is now wholesale and it works with the remote calculation group.

![](https://cdn.sqlbi.com/wp-content/uploads/image6-14.png)

This example was deliberately kept simple, as it used only one calculation that can easily be pushed to the remote server to force it to be wholesale. You should consider that the choice of pushing a calculation onto the remote server or onto the local server is made on sub-expressions. Indeed, look at what might happen in a slightly more complex scenario, where we use the *Discounted Sales (Wrong)* measure as part of a more complex expression that subtracts the discounted sales from *Sales Amount*:

```dax


Discount := [Sales Amount] - [Discounted Sales (Wrong)]

```

In the code of *Discount* there is a wholesale measure (*Sales Amount*) and a retail measure (*Discounted Sales (Wrong)*). Therefore, a remote calculation item is applied to *Sales Amount* but it is not applied to *Discounted Sales (Wrong)*. As such, the result computes the year-to-date of *Sales Amount*, but only the monthly value for *Discounted Sales(Wrong)*. This provides a surprising result that mixes two types of calculations.

![](https://cdn.sqlbi.com/wp-content/uploads/image7-10.png)

As you might guess, this example is still understandable because we built it step by step purposely starting from a measure that did not work. In the real world, you might produce a much more complex piece of DAX code where some sub-expressions are retail and some others are wholesale. This will generate numbers that are ridicously complex to debug and to understand.

The only scenario where calculation groups are expected to work with no bad surprises is when it is guaranteed that the execution of a measure is wholesale. Unfortunately, this is possible only if the composite model does not include any new table in the local VertiPaq model, and the model itself is connected to a single data island. Indeed, calculated columns can be defined only if their execution is wholesale.

If the calculation group were defined in the local model, then it would work on any measure: local or remote, wholesale or retail, with no difference whatsoever.

For these reasons, as a best practice we suggest that you disable the capability to create composite models by connecting to a model in case calculation groups were part of the design of the remote model. In passing, for more information on disabling composite models you should read [Manage DirectQuery connections to a published dataset](https://docs.microsoft.com/en-us/power-bi/connect-data/desktop-discourage-directquery-connections-to-dataset). Clearly, users with a good knowledge of DAX and the difference between wholesale and retail execution could manage to extend any model with a composite model. Nonetheless, if you want to stay on the safe side, it would be better to prohibit extending a remote model that contains calculation groups through a composite model.

[[ALLSELECTED]]

CALCULATE modifier

Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied inside the query, but keeping filters that come from outside.

`ALLSELECTED ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[RELATED]]

Returns a related value from another table.

`RELATED ( <ColumnName> )`

[[LOOKUPVALUE]]

Retrieves a value from a table.

`LOOKUPVALUE ( <Result_ColumnName>, <Search_ColumnName>, <Search_Value> [, <Search_ColumnName>, <Search_Value> [, … ] ] [, <Alternate_Result>] )`

## Articles in the Calculation Groups series

- [Introducing Calculation Groups](https://www.sqlbi.com/articles/introducing-calculation-groups/)
- [Understanding Calculation Groups](https://www.sqlbi.com/articles/understanding-calculation-groups/)
- [Understanding the Application of Calculation Items](https://www.sqlbi.com/articles/understanding-the-application-of-calculation-items/)
- [Understanding Calculation Group Precedence](https://www.sqlbi.com/articles/understanding-calculation-group-precedence/)
- [Controlling Format Strings in Calculation Groups](https://www.sqlbi.com/articles/controlling-format-strings-in-calculation-groups/)
- [Avoiding Pitfalls in Calculation Groups Precedence](https://www.sqlbi.com/articles/avoiding-pitfalls-in-calculation-groups-precedence/)
- [Using calculation groups to selectively replace measures in DAX expressions](https://www.sqlbi.com/articles/using-calculation-groups-to-selectively-replace-measures-in-dax-expressions/)
- [Using calculation groups to switch between dates](https://www.sqlbi.com/articles/using-calculation-groups-to-switch-between-dates/)
- *Understanding the interactions between composite models and calculation groups*
