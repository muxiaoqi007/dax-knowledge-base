---
title: "DAX limitations with inactive relationships and row-level security (RLS)"
url: "https://www.sqlbi.com/articles/dax-limitations-with-inactive-relationships-and-row-level-security-rls"
published: "2024-02-26"
updated: 
source: "sqlbi.com"
---

# DAX limitations with inactive relationships and row-level security (RLS)

> 发布：2024-02-26

[[USERELATIONSHIP]] is a very common and helpful function, used whenever there are multiple relationships between tables and developers need to decide which relationship to use. However, in some scenarios, this common function raises an annoying error:

> **The UseRelationship() and CrossFilter() functions may not be used when querying ‘Sales’ because it is constrained by row-level security.**

As with all the error messages, this requires some understanding and further explanation. Moreover, a workaround is straightforward to find. However, the workaround has some subtle restrictions that need to be well understood.

## Introducing the inactive relationship

We have a semantic model with *Date* as a role dimension: the *Date* table references both *Order Date* and *Delivery Date* in *Sales*. Hence, there are two relationships between *Date* and *Sales*.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-75.png)

We want to produce a simple report that shows, for each year, the amount ordered and the amount delivered. Therefore, we start by authoring the two measures:

Measure in Sales table

```dax


Sales Amount = 

SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )

```

Measure in Sales table

```dax


Delivered Amount =

CALCULATE (

    [Sales Amount],

    USERELATIONSHIP ( Sales[Delivery Date], 'Date'[Date] )

)

```

The *Delivered Amount* measure works fine: it computes *Sales Amount* using [[USERELATIONSHIP]] to activate the inactive relationship between *Sales* and *Date* based on *Sales[Delivery Date]* instead of *Sales[Order Date]*.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-72.png)

Before moving further, please note that we use a slightly modified version of our standard Contoso database. We removed all orders placed in 2020 and increased the delivery time. Indeed, there are no sales in 2020 despite several delivered orders. These are orders placed in 2019 and delivered in 2020. This detail is going to be useful later in the article. Remember that we show the example using the *Date* table, but the same issue can arise when you apply security on other tables like *Product* and *Customer*.

## Introducing the security model

We might encounter a serious issue if we activate the row-level security on the semantic model. To demonstrate this, we added the requirement that not all users are authorized to see the data for all years. Each user is associated with a set of allowed years. We can easily implement the requirement through two additional tables.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-64.png)

We secure the *Users* table to show only users whose *UserId* equals [[USERPRINCIPALNAME]] and we use *UserYears* to filter *Date*.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-58.png)

*UserYears* is helpful to let us associate a single user with multiple years. In our example, both Marco and Alberto can see two years.

![](https://cdn.sqlbi.com/wp-content/uploads/image5-50.png)

Let us quickly recap the security settings. The *Users* table is secured; the filter moves from *Users* to *UserYears* through a regular relationship and then from *UserYears* to *Date* through a limited many-to-many relationship. At the end, the user filters the year, hence the dates.

We can test security using the View As feature of Power BI.

![](https://cdn.sqlbi.com/wp-content/uploads/image6-43.png)

Unfortunately, the report is no longer working, raising an error.

![](https://cdn.sqlbi.com/wp-content/uploads/image7-35.png)

Role-based security does not filter *Sales* directly; it filters *Sales* indirectly through the chain of relationships described above. The problem is that we use the relationship between *Date* and *Sales[Order Date]* to apply security, and in the *Delivered Amount* measure, we change the active relationship.

If DAX allowed developers to change the active relationship, this could lead to security holes. Therefore, if a relationship transfers a security filter, then the relationship cannot be overridden through [[USERELATIONSHIP]].

As with any security settings, the engine enforces this restriction: there are no ways to avoid it. Better safe than sorry.

## Fixing the error

However, this does not mean that there are no ways to author the *Delivered Amount* measure. Transferring filters from one table to another is possible without a relationship. As a matter of fact, we described several techniques to move filters in this article, [Propagating filters using TREATAS in DAX](https://www.sqlbi.com/articles/propagate-filters-using-treatas-in-dax/).

In our scenario, we can author *Delivered Amount* by using [[TREATAS]]:

Measure in Sales table

```dax


Delivered Amount 2 =

CALCULATE (

    [Sales Amount],

    ALL ( 'Date' ),

    TREATAS (

        VALUES ( 'Date'[Date] ),

        Sales[Delivery Date]

    )

)

```

The code requires some explanation because – despite being short – it hides some subtleties worth describing.

[[TREATAS]] reads the content of *Date[Date]* in the current filter context through [[VALUES]] and changes its lineage to *Sales[Delivery Date]*. Doing this moves the current filter on *Date* to *Sales[Delivery Date]*, precisely as a relationship would.

The measure also includes [[ALL]] over *Date*. The reason is that the relationship between *Date* and *Sales* is still active and based on *Sales[Order Date]*. Therefore, the filter on *Date* is moved to *Sales[Order Date],* and – because we want to filter *Sales[Delivery Date]* instead of *Sales[Order Date]* – we must get rid of the automatic filter propagation. Hence, we use [[ALL]] to remove the filter over *Date,* and consequently on *Sales[Order Date]*. Once the filter is removed, [[TREATAS]] filters *Sales[Delivery Date]*, returning the desired result.

![](https://cdn.sqlbi.com/wp-content/uploads/image8-29.png)

The report only shows two years because Alberto can inspect only 2017 and 2018 — the same report, viewed as Marco, shows different years.

![](https://cdn.sqlbi.com/wp-content/uploads/image9-27.png)

The careful reader should be frowning at this point: something weird is happening. Orders were placed in 2019 and delivered in 2020, as shown in the introduction. However, the report does not show the delivered amount for these orders.

If the goal was to hide the orders made in 2020 without hiding the amount delivered in that same year, the security constraints have not been placed correctly. The problem has nothing to do with DAX or Power BI; the issue concerns the data model itself. By placing the security filter over the *Year* column in *Date*, we restrict the *Date* table, not just the order date. *Date* and *Order Date* are not the same entity. They would be if only one relationship linking *Date* and *Sales* existed. Because there are two relationships, *Date* has multiple meanings: it can filter *Order Date* or *Delivery Date*, depending on the specific measure.

This article is not about modeling security correctly; therefore, we do not want to analyze the issue in detail. However, it would be cruel not to provide a simple solution, in hopes to increase our readers’ drive to study more about data modeling and security.

## Alternative approaches to security filters

A possible solution is to place the filter on *Sales*, filtering the *Sales[Order Date]* column. This is not an efficient security filter from a performance point of view; it is just an example of a different approach to the security filter:

Security filter in Sales table

```dax


YEAR ( Sales[Order Date] ) IN

    CALCULATETABLE (

        VALUES ( UserYears[Year] ),

        Users[UserId] == USERPRINCIPALNAME ( )

    )

```

By using this filter on the *Sales* table and turning off the relationship between *UserYears* and *Date*, we obtain the following report for Marco.

![](https://cdn.sqlbi.com/wp-content/uploads/image10-24.png)

Now, the role-based security filters *Sales[Order Date]*, and the report shows the deliveries in 2020 despite Marco not having access to orders placed in 2020. Moreover, because security is now in the right place, there is no need to filter *Sales* through *Date*, and the original *Delivered Amount* measure works fine.

![](https://cdn.sqlbi.com/wp-content/uploads/image11-17.png)

There would be a lot to consider when it comes to performance optimization, but this is not the right place to dive deeper into the topic. Indeed, the goal of this article is to focus only on [[USERELATIONSHIP]] and on the RLS error.

## Conclusions

Row-level security placed on a dimension with multiple relationships may create problems when activating an inactive relationship. We can avoid the error by using [[TREATAS]] or other means of moving the filter between tables. However, be very mindful that when using [[TREATAS]], you need to ensure that the row-level security semantics are kept intact.

In our scenario, forcing the filter through [[TREATAS]] resulted in incomplete reports, hiding values that should have been displayed.

Security is an important topic that requires good modeling skills. In scenarios like this, the DAX error indicates that something much worse than a DAX error may happen – like disclosing data that should be secured. However, [[TREATAS]] can be a good workaround for the DAX error if you validate the semantics that it defines.

[[USERELATIONSHIP]]

CALCULATE modifier

Specifies an existing relationship to be used in the evaluation of a DAX expression. The relationship is defined by naming, as arguments, the two columns that serve as endpoints.

`USERELATIONSHIP ( <ColumnName1>, <ColumnName2> )`

[[USERPRINCIPALNAME]]

Returns the user principal name.

`USERPRINCIPALNAME ( )`

[[TREATAS]]

Treats the columns of the input table as columns from other tables. For each column, filters out any values that are not present in its respective output column.

`TREATAS ( <Expression>, <ColumnName> [, <ColumnName> [, … ] ] )`

[[VALUES]]

When a column name is given, returns a single-column table of unique values. When a table name is given, returns a table with the same columns and all the rows of the table (including duplicates) with the [additional blank row](https://www.sqlbi.com/articles/blank-row-in-dax/) caused by an invalid relationship if present.

`VALUES ( <TableNameOrColumnName> )`

[[ALL]]

CALCULATE modifier

Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied.

`ALL ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`
