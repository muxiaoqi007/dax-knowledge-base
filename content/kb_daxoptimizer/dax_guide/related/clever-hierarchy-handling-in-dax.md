---
title: "Clever Hierarchy Handling in DAX"
url: "https://www.sqlbi.com/articles/clever-hierarchy-handling-in-dax"
published: "2020-11-11"
updated: 
source: "sqlbi.com"
---

# Clever Hierarchy Handling in DAX

> 发布：2020-11-11

**UPDATE 2020-11-11**: You can find **more complete detailed and optimized examples for this calculation in the [DAX Patterns: Hierarchies](https://www.daxpatterns.com/hierarchies/) article+video** on [daxpatterns.com](https://www.daxpatterns.com/).

That said, there are several scenarios where it might be useful to handle hierarchies in DAX and, in this post, I am going to show some techniques to make them work in a clever way, starting from the basics until we reach a good level of hierarchy understanding in DAX.

Let us start with the business scenario. Using AdventureWorks, you can easily build a PivotTable like the following one, creating some measures that compute the ratio of

- a single product against its subcategory
- a single subcategory over its category
- a single category over the whole of the products

In MDX, we would have simply said the “ratio to parent”. The general pattern of these formulas is pretty easy, in the following piece of code you can see the code of RatioToSubcategory:

```dax


RatioToSubcategory =

SUM (Sales[SalesAmount])

/

CALCULATE (

    SUM (Sales[SalesAmount]),

    ALL (Products, Products[ProductName])

)

```

For the RatioToSubcategory you divide the sales amount by the total sales amount of the subcategory, by simply freeing the constraint over the product name using [[ALL]]. The other two formulas follow the same pattern and are not worth showing. If you put these measures in a PivotTable, you get a result like this:

[![image](https://cdn.sqlbi.com/wp-content/uploads/image_thumb14.png "image")](https://cdn.sqlbi.com/wp-content/uploads/image14.png)

While it looks nice, there are several issues with this report:

- The RatioToSubcategory makes sense only at the product level, there is no point to show 100% at the subcategory level and, worse, the number at the category level makes no sense at all, because it contains a wrong number.
- It would be really nice to show all these numbers in a single column, instead of having three columns, two of which need always to be cleared.

I have highlighted with the yellow color the useful information, all other numbers should be hidden, because they have no meaning.

Luckily, in SQL 2012, DAX has been enriched with the [[ISFILTERED]] function, which lets you detect whether a column has been filtered or not. [[ISFILTERED]] is your best friend when it comes to detect which level of a hierarchy you are browsing. For example, to detect whether you are at the product level, it is enough to check whether the product name has been filtered and make your formula behave accordingly. For example, you can make a smarter version of the RatioToSubcategory that automatically detects whether you are browsing the hierarchy at the product level and BLANKs the value if this is not the case. The updated code of RatioToSubcategory is the following:

```dax


IF (

    ISFILTERED (Products[ProductName]),

    SUM (Sales[SalesAmount])

    /

    CALCULATE (

        SUM (Sales[SalesAmount]),

        ALL (Products[ProductName])

    )

)

```

At the product level, everything works fine but, at the category and subcategory level, we need to make more complex tests in order to check whether we are exactly at the correct level. For example, to check whether we are showing some data at the subcategory level, we need to verify that there is a filter on the subcategory and, at the same time, there is no filter on product name (otherwise, if there is a filter on the product name, we are at the product level). Thus, the code is a little bit tricky:

```dax


IF (

    ISFILTERED (Products[Subcategory]) && NOT (ISFILTERED (Products[ProductName])),

    SUM (Sales[SalesAmount])

    /

    CALCULATE (

        SUM (Sales[SalesAmount]),

        ALL (Products[Subcategory])

    )

)
```

Making this update to all of our measures, we get this nicer results, where useless values disappeared:

[![image](https://cdn.sqlbi.com/wp-content/uploads/image_thumb15.png "image")](https://cdn.sqlbi.com/wp-content/uploads/image15.png)

Not exactly sexy, but at least it does not contain any wrong value. In order to make it nicer, we can now combine everything in a single measure, which shows the percentage over the parent in a single column. The code is a bit longer, but it does its job:

```dax


RatioToParent :=

IF (

    ISFILTERED (Products[ProductName]),

    SUM (Sales[SalesAmount])

    /

    CALCULATE (

        SUM (Sales[SalesAmount]),

        ALL (Products[ProductName])

    ),

    IF (

        ISFILTERED (Products[Subcategory]),

        SUM (Sales[SalesAmount])

        /

        CALCULATE (

            SUM (Sales[SalesAmount]),

            ALL (Products[Subcategory])

        ),

        IF (

            ISFILTERED (Products[Category]),

            SUM (Sales[SalesAmount])

            /

            CALCULATE (

                SUM (Sales[SalesAmount]),

                ALL (Products[Category])

            )

        )

    )

)

```

And the result looks what we wanted from the beginning. In the following picture you can see all the measures together even if the RatioToParent will be the only visible measure in a real-world report.

[![image](https://cdn.sqlbi.com/wp-content/uploads/image_thumb16.png "image")](https://cdn.sqlbi.com/wp-content/uploads/image16.png)

As nice as it looks, this formula has a major drawback. If you add a slicer on ProductName to the PivotTable, for example, the formula stops working and shows meaningless values. In the next figure you can see that a slicer which selected “Mountain Bottle Cage” and “Road Bottle Cage” transformed the numbers in a hard-to-understand way:

[![image](https://cdn.sqlbi.com/wp-content/uploads/image_thumb17.png "image")](https://cdn.sqlbi.com/wp-content/uploads/image17.png)

The problem is that the formula, in order to detect the hierarchy level, in reality checks for the existence of a filter on the ProductName column. Due to the presence of the slicer, the ProductName is always filtered. In fact, as you can easily note from the figure, the RatioToSubcategory column always contain a value, while RatioToCategory and RatioToAll are always empty. I leave as an interesting exercise to the reader the explanation of what numbers are shown at the category and subcategory level: they have a meaning but I bet that no user will be happy with what their represent.

How do we solve this issue? Well, it is pretty easy, indeed, even if not very intuitive. The problem is that we are leaving the ability to the user to filter the columns on our hierarchy and this interacts with our hierarchy detection system. A possible solution would be to hide the columns in the hierarchy but this turns out to be too restrictive: users like slicers, and they have good reason to set filters. But, thinking carefully, users want to filter product names, not necessarily the same product names we are using on the hierarchy. Thus, we are searching for a way to let users filter names without filtering our hierarchy. You can reach this goal duplicating all of the columns you use in the hierarchy and hiding the ones in the hierarchy.

In the next figure, you can see the new data model. Category, Subcategory and ProductName have been copied in new columns, called HCategory, HSubcategory and HProductName. These last columns have been used to create the hierarchy and have been hidden from the client tools, so that the user has no way to use them in slicers. The only way to put a filter on these columns is to use the hierarchy. On the other hand, the user can still filter the product names using the original columns.

[![image](https://cdn.sqlbi.com/wp-content/uploads/image_thumb18.png "image")](https://cdn.sqlbi.com/wp-content/uploads/image18.png)

It is important to note that, from the calculation point of view, filtering either Category or HCategory leads to the same result. From the filter context point of view, on the other hand, there is a big difference between filtering one or the other, and we take advantage of this.

The formula needed to be updated, of course:

```dax


RatioToParent :=

IF (

    ISFILTERED (Products[HProductName]),

    SUM (Sales[SalesAmount])

    /

    CALCULATE (

        SUM (Sales[SalesAmount]),

        ALL (Products[HProductName])

    ),

    IF (

        ISFILTERED (Products[HSubcategory]),

        SUM (Sales[SalesAmount])

        /

        CALCULATE (

            SUM (Sales[SalesAmount]),

            ALL (Products[HSubcategory])

        ),

        IF (

            ISFILTERED (Products[HCategory]),

            SUM (Sales[SalesAmount])

            /

            CALCULATE (

                SUM (Sales[SalesAmount]),

                ALL (Products[HCategory])

            )

        )

    )

)

```

Now, the same PivotTable as before, with the new formula, shows meaningful values:

[![image](https://cdn.sqlbi.com/wp-content/uploads/image_thumb19.png "image")](https://cdn.sqlbi.com/wp-content/uploads/image19.png)

The formula now checks the HProductName column, which is invisible to the user but still available in DAX code and does the same for HCategory and HSubcategory. If you don’t tell the user about this trick, he will never see any difference between the column names in the hierarchy and the ones available for filtering, leading to a very nice user experience.

The only drawback is that this formula will work if and only if the user uses the hierarchy on rows or columns, othwerwise, if he places the category column on rows, the formula will not work, since placing the category on rows does not create any filter on the HCategory column. This is not usually an issue, it is just something to tell users to be aware of.

At this point, you might guess why I did not use the Excel built-in function to obtain the same result. The reason is quite simple: while Excel makes it easy to compute the ratio over parent, this technique has a much greater flexibility, because it defines the logic in the formula and does not require any user intervention to work. Moreover, the same technique of duplicating columns in hierarchies can prove useful in many different scenario, not natively handled by Excel.

As it often happens, Tabular does not have all of the features of Multidimensional but, by means of playing with filter contexts, you have the option to reproduce the same behavior of Multidimensional, still experiencing the tremendous power of Vertipaq (ops, of the xVelocity in-memory Analytics Engine).

[[ALL]]

CALCULATE modifier

Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied.

`ALL ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[ISFILTERED]]

Returns true when there are direct filters on the specified column.

`ISFILTERED ( <TableNameOrColumnName> )`
