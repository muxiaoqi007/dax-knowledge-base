---
title: "Using join functions in DAX"
url: "https://www.sqlbi.com/articles/using-join-functions-in-dax"
published: "2023-12-18"
updated: 
source: "sqlbi.com"
---

# Using join functions in DAX

> 发布：2023-12-18

Readers with knowledge of SQL know that the join operation is widespread in SQL queries, as it is the standard way to combine data stored in different tables. It is however uncommon to explicitly join tables in DAX because the relationships in the data model provide enough information to allow many DAX functions to work without an explicit join operation. Most of the time, the join between tables is implicit and automatic.

However, DAX has two explicit join functions: [[NATURALLEFTOUTERJOIN]] and [[NATURALINNERJOIN]]. Apparently, these functions correspond to the behavior of [[LEFT]] OUTER JOIN and INNER JOIN in SQL. However, they differ from SQL in how you specify the join condition. This article shows how these functions can be used in DAX with practical examples. If you need a more introductory article about the syntax of these functions, read [From SQL to DAX: Joining Tables](https://www.sqlbi.com/articles/from-sql-to-dax-joining-tables/), where we compare the SQL syntax with similar DAX functions.

In the following examples, we will show how to combine tables like products, subcategories, categories, and sales, producing a single table as a result that requires multiple tables to produce the expected result. While there are often more efficient ways to obtain the same result in DAX using other functions, we use these examples to show the result of the join functions in DAX: do not consider them as a best practice to obtain the corresponding result, but just as an educational tool. The join functions can be useful in more complex scenarios when you cannot rely on relationships, as we will [show in a separate article](https://www.sqlbi.com/articles/replacing-relationships-with-join-functions-in-dax/).

**IMPORTANT**: [[NATURALLEFTOUTERJOIN]] and [[NATURALINNERJOIN]] use **only regular relationships**. The limited relationships like many-to-many or between different data islands are not used as a join condition by these functions. In other words, [[NATURALLEFTOUTERJOIN]] and [[NATURALINNERJOIN]] only consider the relationships that are part of an expanded table.

## Simple joins

We start with an example where the *Product* table has a many-to-one relationship with the *SubCategories* table. We want to obtain a table where every product name has the corresponding subcategory name in another column.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-70.png)

By using the [[NATURALLEFTOUTERJOIN]] function, we get a result with all the rows in *Product* and all the columns of both tables:

```dax


EVALUATE

NATURALLEFTOUTERJOIN ( 'Product', SubCategories )

```

The result only includes the subcategories with at least one product; if a product does not have a corresponding subcategory, the corresponding *SubCategories* columns are blank. To make the result easier to read, we can limit the output to two columns:

```dax


EVALUATE

SELECTCOLUMNS (

    NATURALLEFTOUTERJOIN ( 'Product', SubCategories ),

    'Product'[Product Name],

    SubCategories[Subcategory]

)

```

The result includes products with a blank subcategory.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-67.png)

The [[NATURALLEFTOUTERJOIN]] syntax is simple, as it implicitly uses the existing relationship between *Product* and *SubCategories* to define the join condition. However, [[NATURALLEFTOUTERJOIN]] is not common in DAX because the same result can be obtained with the following syntax:

```dax


EVALUATE

SELECTCOLUMNS (

    'Product',

    'Product'[Product Name],

    RELATED ( SubCategories[Subcategory] )

)

```

If *Product Name* is unique, then you can also use [[SUMMARIZE]]:

```dax


EVALUATE

SUMMARIZE (

    'Product',

    'Product'[Product Name],

    SubCategories[Subcategory]

)

```

By using [[NATURALINNERJOIN]], the result has the products with a corresponding subcategory only, returning 2,305 rows instead of 2,517:

```dax


EVALUATE

SELECTCOLUMNS (

    NATURALINNERJOIN ( 'Product', SubCategories ),

    'Product'[Product Name],

    SubCategories[Subcategory]

)

```

Once again, the same result can be achieved with other DAX functions. For example, [[GENERATE]] only returns the existing rows in *SubCategories* that have at least one corresponding row in *Product*:

```dax


EVALUATE

SELECTCOLUMNS (

    GENERATE ( SubCategories, RELATEDTABLE ( 'Product' ) ),

    'Product'[Product Name],

    SubCategories[Subcategory]

)

```

## Cascading one-to-many

When we have more than two tables involved in relationships, we must figure out the proper join order when we use [[NATURALLEFTOUTERJOIN]] and [[NATURALINNERJOIN]] in DAX. These two functions only get two arguments, so defining the order is important regarding the missing rows on the one-side of a relationship (the additional blank row for invalid relationships).

We are now considering the three tables *SalesSub*, *SubCategories*, and *Categories*. We want to obtain a table where for each category and subcategory we have the amount of sales in every year.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-59.png)

The [[NATURALLEFTOUTERJOIN]] and [[NATURALINNERJOIN]] functions use the relationships in the model. If we start from *Categories*, we include all the categories regardless of the presence of data in the other tables (*Subcategories* and *SalesSub*):

```dax


EVALUATE

NATURALLEFTOUTERJOIN (

    NATURALLEFTOUTERJOIN ( Categories, SubCategories ),

    SalesSub

)

ORDER BY

    Categories[Category Code],

    Subcategories[Subcategory Code],

    SalesSub[Year],

    SalesSub[Amount]

```

The result includes categories without subcategories (like 07 Games and Toys) and subcategories without transactions in *SalesSub* (like 0106 Bluetooth Headphones).

![](https://cdn.sqlbi.com/wp-content/uploads/image4-53.png)

[[NATURALLEFTOUTERJOIN]] can be used with two tables that are not directly connected with one relationship; if the two tables are far away from each other, it works by using the entire chain. However, the result is different, compared with the previous example where we used all three tables. For example, by joining *Categories* and *SalesSub*, the result does not have rows and columns of the *Subcategories* table:

```dax


EVALUATE

NATURALLEFTOUTERJOIN ( Categories, SalesSub )

ORDER BY

    Categories[Category Code],

    SalesSub[Subcategory Code],

    SalesSub[Year],

    SalesSub[Amount]

```

The result shows the Games and Toys category without any subcategory or amount, but it does not include the Bluetooth Headphones subcategory returned by the previous query.

![](https://cdn.sqlbi.com/wp-content/uploads/image5-45.png)

However, regular relationships create a blank row in *Categories* and *Subcategories* if there are subcategories without a matching category or rows in *SalesSub* without a matching subcategory, respectively. These additional blank rows can be made visible by using [[VALUES]]. In this example, we do not apply [[VALUES]] to *SalesSub* because the table is never on the one side of a one-to-many relationship, so there is no additional blank row in *SalesSub*:

```dax


EVALUATE

NATURALLEFTOUTERJOIN ( VALUES ( Categories ), SalesSub )

ORDER BY

    Categories[Category Code],

    SalesSub[Subcategory Code],

    SalesSub[Year] ,

    SalesSub[Amount]

```

The result includes a blank row for *Categories* that joins all the *SalesPub* rows that do not have a matching row in *Subcategories* or *Categories*. The result still includes the Games and Toys category with a blank *Amount* value.

![](https://cdn.sqlbi.com/wp-content/uploads/image6-40.png)

If you are used to SQL, a more intuitive variation is to use the *SalesSub* table as a first argument of the [[NATURALLEFTOUTERJOIN]] function. This way, the result is very similar to the previous example using [[VALUES]] for *Categories* in the first argument, with the only exception that it does not include rows in *Categories* that have no corresponding data in *Subcategories* and *SalesSub*:

```dax


EVALUATE

NATURALLEFTOUTERJOIN ( SalesSub, Categories )

ORDER BY

    Categories[Category Code],

    SalesSub[Subcategory Code],

    SalesSub[Year],

    SalesSub[Amount]

```

The only difference between the last two results is that the latter does not include the category Games and Toys.

![](https://cdn.sqlbi.com/wp-content/uploads/image7-32.png)

The last result is the same as what we can obtain by using the more popular (for DAX) [[SUMMARIZE]] function:

```dax


EVALUATE

SUMMARIZE (

    SalesSub,

    Categories[Category],

    Categories[Category Code],

    SalesSub[Subcategory Code],

    SalesSub[Year],

    SalesSub[Amount]

)

ORDER BY

    Categories[Category Code],

    SalesSub[Subcategory Code],

    SalesSub[Year],

    SalesSub[Amount]

```

While [[SUMMARIZE]] is more common in DAX, it only works with relationships between tables. The join functions in DAX can also be used without relationships, but in that case, the join condition is applied to columns with the same data lineage regardless of the column name or the same name and no data lineage. We complete this article by describing the latter case: how to join tables (usually stored in variables) with no data lineage. We will dedicate a separate article to how to join tables of the data model that do not have relationships.

## Joining temporary tables

[[NATURALLEFTOUTERJOIN]] and [[NATURALINNERJOIN]] use the column name for the join condition only for the columns without a data lineage. For example, the result of expressions applied to variables that do not transfer the data lineage from the data model can be joined by matching the rows with the same value in one or more columns with the same name. Even though we do not have a relationship, the join condition is always implicit in the request.

For example, consider two tables (*\_Products* and *\_Subcategories*) that we want to combine into a single table where for each product name we can see the corresponding subcategory name in another column. The following query joins two variables (one for each table) with a common column name, *Subcategory Code*:

```dax


DEFINE

    VAR _Products =

        SELECTCOLUMNS (

            {

                ( "Contoso Bright Light battery E20 Black", "0308" ),

                ( "Contoso Bright Light battery E20 blue", "0308" ),

                ( "Fabrikam Microwave 0.8CuFt E0800 Silver", "0803" )

            },

            "Product Name", [Value1],

            "Subcategory Code", [Value2]

        )

    VAR _Subcategories =

        SELECTCOLUMNS (

            { ( "Computers Accessories", "0308" ), ( "Digital Cameras", "0401" ) },

            "Subcategory", [Value1],

            "Subcategory Code", [Value2]

        )



EVALUATE

NATURALLEFTOUTERJOIN ( _Products, _Subcategories )



EVALUATE

NATURALLEFTOUTERJOIN ( _Subcategories, _Products )



EVALUATE

NATURALINNERJOIN ( _Subcategories, _Products )

```

The first [[NATURALLEFTOUTERJOIN]] result includes all the rows in *\_Products* with the corresponding *Subcategory* value retrieved from the *\_Subcategories* variable. The subcategory code 0803 does not exist in the *\_Subcategories* variable, so the *Subcategory* column is blank for that row.

![](https://cdn.sqlbi.com/wp-content/uploads/image8-26.png)

The second [[NATURALLEFTOUTERJOIN]] result shows all the subcategories with the corresponding products, including the subcategory code 0401, which does not have any corresponding product.

![](https://cdn.sqlbi.com/wp-content/uploads/image9-24.png)

The last result corresponds to [[NATURALINNERJOIN]] and includes only the rows in *\_Products* with a corresponding row in *\_Subcategory*, without including products and subcategories that do not match on the other table.

![](https://cdn.sqlbi.com/wp-content/uploads/image10-21.png)

The join functions in DAX use all the matching columns. While this could be uncommon when used with the relationships (as they are based on a single column), it becomes interesting when we join tables using columns without a data lineage. Therefore, [[NATURALLEFTOUTERJOIN]] and [[NATURALINNERJOIN]] can **join two tables by using two or more columns**.

The following example joins the *\_Products* and *\_Subcategories* variables by using the two columns *Category Code* and *Subcategory Code*, which exist in both variables:

```dax


DEFINE

    VAR _Products =

        SELECTCOLUMNS (

            {

                ( "Contoso Home Theater System 2.1 Channel E1200 Brown", "02", "03" ),

                ( "Litware Home Theater System 4.1 Channel M410 Brown", "02", "03" ),

                ( "Contoso Washer & Dryer 27in L270 Blue", "08", "01" ),

                ( "Contoso Microwave 1.5CuFt X0110 Red", "08", "03" ),

                ( "Adventure Works 32"" LCD HDTV M130 Black", "02", "01" ),

                ( "Contoso Bright Light battery E20 Black", "03", "08" )

            },

            "Product Name", [Value1],

            "Category Code", [Value2],

            "Subcategory Code", [Value3]

        )

    VAR _Subcategories =

        SELECTCOLUMNS (

            {

                ( "Computers", "02", "Laptops", "01" ),

                ( "Computers", "02", "Desktops", "03" ),

                ( "Home Appliances", "08", "Washers & Dryers", "01" ),

                ( "Home Appliances", "08", "Microwaves", "03" ),

                ( "Home Appliances", "08", "Lamps", "06" )

            },

            "Category", [Value1],

            "Category Code", [Value2],

            "Subcategory", [Value3],

            "Subcategory Code", [Value4]

        )



EVALUATE

NATURALLEFTOUTERJOIN ( _Products, _Subcategories )



EVALUATE

NATURALLEFTOUTERJOIN ( _Subcategories, _Products )



EVALUATE

NATURALINNERJOIN ( _Subcategories, _Products )

```

As in the previous example, the first [[NATURALLEFTOUTERJOIN]] result includes all the rows in *\_Products* with the corresponding *Subcategory* value retrieved from the *\_Subcategories* variable. Please note that if the matching had been based on a single column (like *Category Code*), the result would have produced many more rows, combining all the subcategories of a category with all the products of the same category – just as the case would be in SQL with an incomplete join condition. In this case, there is no match for category 03 and subcategory 08 in the *\_Subcategories* variable, so the *Subcategory* column is blank for that row.

![](https://cdn.sqlbi.com/wp-content/uploads/image11-14.png)

The second [[NATURALLEFTOUTERJOIN]] result shows all the subcategories with the corresponding products, including the Lamps subcategory (category 08 and subcategory 06) with no corresponding product.

![](https://cdn.sqlbi.com/wp-content/uploads/image12-10.png)

The last [[NATURALINNERJOIN]] shows only products with matching subcategories.

![](https://cdn.sqlbi.com/wp-content/uploads/image13-11.png)

## Conclusions

The [[NATURALLEFTOUTERJOIN]] and [[NATURALINNERJOIN]] functions in DAX might be helpful to return all the columns of two tables connected by a chain of one-to-many relationships. Most of the time, the same result can be achieved by using other DAX functions that implicitly use the relationships of the data model. However, if you have tables containing data without a data lineage, then the join uses all the columns with the same name in the joined tables, making it possible to join two tables using two or more columns. Applying the same technique to tables that are part of the data model is complex and generally slow, so it is always better to create a column for a regular relationship by combining multiple columns when that is the join requirement.

[[NATURALLEFTOUTERJOIN]]

Joins the Left table with right table using the Left Outer Join semantics.

`NATURALLEFTOUTERJOIN ( <LeftTable>, <RightTable> )`

[[NATURALINNERJOIN]]

Joins the Left table with right table using the Inner Join semantics.

`NATURALINNERJOIN ( <LeftTable>, <RightTable> )`

[[LEFT]]

Returns the specified number of characters from the start of a text string.

`LEFT ( <Text> [, <NumberOfCharacters>] )`

[[SUMMARIZE]]

Creates a summary of the input table grouped by the specified columns.

`SUMMARIZE ( <Table> [, <GroupBy_ColumnName> [, [<Name>] [, [<Expression>] [, <GroupBy_ColumnName> [, [<Name>] [, [<Expression>] [, … ] ] ] ] ] ] ] )`

[[GENERATE]]

The second table expression will be evaluated for each row in the first table. Returns the crossjoin of the first table with these results.

`GENERATE ( <Table1>, <Table2> )`

[[VALUES]]

When a column name is given, returns a single-column table of unique values. When a table name is given, returns a table with the same columns and all the rows of the table (including duplicates) with the [additional blank row](https://www.sqlbi.com/articles/blank-row-in-dax/) caused by an invalid relationship if present.

`VALUES ( <TableNameOrColumnName> )`
