---
title: "Replacing relationships with join functions in DAX"
url: "https://www.sqlbi.com/articles/replacing-relationships-with-join-functions-in-dax"
published: "2024-01-01"
updated: 
source: "sqlbi.com"
---

# Replacing relationships with join functions in DAX

> 发布：2024-01-01

In a previous article, we saw several examples of [using the NATURALLEFTOUTERJOIN and NATURALINNERJOIN functions](https://www.sqlbi.com/articles/using-join-functions-in-dax/) in DAX. In that article, we saw how to join tables in the data model using existing relationships. However, if we want to join tables in the model without using the relationships in the model, we must eliminate the data lineage of the columns to use in the join condition. This article describes how to achieve this.

## Joining tables without relationships

We use these three tables without any physical relationship between them, in the following examples.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-71.png)

If we try to join *Categories* and *SubCategories*, we get an error:

```dax


EVALUATE

NATURALLEFTOUTERJOIN (

    Categories,

    SubCategories

)

// ERROR: No common join columns detected. 

// The join function 'NATURALLEFTOUTERJOIN' requires at least one common join column.

```

In the [previous article](https://www.sqlbi.com/articles/using-join-functions-in-dax/), we saw how the presence of columns with the same name and without a data lineage would make it possible to join two tables without a relationship. However, we used variables where none of the columns had a data lineage in that example. When we play with model columns, the data lineage propagates when assigning a simple reference to a new column.

Adding a column with the same name in two different tables is not enough if it is populated with just a column reference; indeed, the new column inherits the data lineage of the column reference and fails the join:

```dax


EVALUATE

NATURALLEFTOUTERJOIN (

    ADDCOLUMNS ( Categories, "@CategoryCode", Categories[Category Code] ),

    ADDCOLUMNS (

        SubCategories,

        "@CategoryCode", SubCategories[Category Code]

    )

)

// ERROR: An incompatible join column, (''[@CategoryCode]) was detected. 

// 'NATURALLEFTOUTERJOIN' doesn't support joins by using columns with different data types or lineage.

```

We need an expression that breaks the data lineage of the column reference to get a column that the join functions can use. A simple string concatenation with an empty string is enough; the [[CONVERT]] function also produces the same effect:

```dax


EVALUATE

NATURALLEFTOUTERJOIN (

    ADDCOLUMNS ( Categories, "@CategoryCode", Categories[Category Code] & "" ),

    ADDCOLUMNS (

        SubCategories,

        "@CategoryCode", CONVERT ( SubCategories[Category Code], STRING )

    )

)

```

The result includes all the columns of *Categories* and *SubCategories*, plus a single *@CategoryCode* column used as a join condition. The content of *@CategoryCode* corresponds to the table on the left of the join: Category code 07 has no subcategories, so the corresponding subcategories columns are blank.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-68.png)

## Joining variables by removing data lineage

When you store the result of table functions in variables, you can still rely on the model relationships if the data lineage is preserved. However, two columns with the same name in two tables cannot be used as a join condition if the data lineage is present. We face the same issue as the one described in the previous example, and we must remove the data lineage and rename the columns to join the variables.

For example, we want a report showing the number of customers and stores in each country. Both the *Customer* and *Store* tables have a *Country* column, followed by the number of customers and stores, respectively. We create two variables, *\_CustomersByCountry* and *\_StoresByCountry*, using the [[ADDCOLUMNS]] function to aggregate the number of customers and stores by country:

```dax


DEFINE

    MEASURE Customer[# Customers] =

        COUNTROWS ( Customer )

    MEASURE Store[# Stores] =

        COUNTROWS ( Store )

    VAR _CustomersByCountry =

        ADDCOLUMNS (

            VALUES ( Customer[Country] ),

            "# Customers", [# Customers]

        )

    VAR _StoresByCountry =

        ADDCOLUMNS (

            VALUES ( Store[Country] ),

            "# Stores", [# Stores]

        )

```

The *\_CustomersByCountry* variable generates a table listing each country from the *Customer* table, along with the corresponding count of customers in *# Customers*. The *\_CustomersByCountry* variable is crucial for our analysis as it forms one-half of the data we wish to join:

```dax


EVALUATE

_CustomersByCountry

```

![](https://cdn.sqlbi.com/wp-content/uploads/image3-60.png)

Similarly, *\_StoresByCountry* creates a table listing each country from the *Store* table, alongside the number of stores in each. This variable represents the other half of the data we aim to join with *\_CustomersByCountry*:

```dax


EVALUATE

_StoresByCountry

```

![](https://cdn.sqlbi.com/wp-content/uploads/image4-54.png)

By looking at the content of the two variables, we would like to join the two tables by using the *Country* columns as a join condition. We wrote the examples by using [[NATURALLEFTOUTERJOIN]], but we would see the same result (an error) with [[NATURALINNERJOIN]]:

```dax


EVALUATE

NATURALLEFTOUTERJOIN (

    _CustomersByCountry,

    _StoresByCountry

)

```

The join between *\_CustomersByCountry* and *\_StoresByCountry* results in an error. The [[NATURALLEFTOUTERJOIN]] function fails to detect common join columns, as the *Country* columns from both variables still have their original data lineage:

`No common join columns detected. The join function 'NATURALLEFTOUTERJOIN' requires at-least one common join column.`

To resolve this, we redefine our variables by using the [[SELECTCOLUMNS]] function. Here, we concatenate an empty string to the value of the *Country* column, effectively breaking the data lineage. This manipulation allows the join function to recognize the new *@Country* columns as common join columns:

```dax


DEFINE

    MEASURE Customer[# Customers] =

        COUNTROWS ( Customer )

    MEASURE Store[# Stores] =

        COUNTROWS ( Store )

    VAR _CustomersByCountry =

        SELECTCOLUMNS (

            VALUES ( Customer[Country] ),

            "@Country", Customer[Country] & "",

            "# Customers", [# Customers]

        )

    VAR _StoresByCountry =

        SELECTCOLUMNS (

            VALUES ( Store[Country] ),

            "@Country", Store[Country] & "",

            "# Stores", [# Stores]

        )



EVALUATE

NATURALLEFTOUTERJOIN (

    _CustomersByCountry,

    _StoresByCountry

)

```

The join output is a comprehensive table that includes each country, the number of customers, and the number of stores in that country. The “Online” value is not present because it only exists in *\_StoresByCountry* and not in *\_CustomerByCountry*, which is used to return the *@Country* values in the result.

![](https://cdn.sqlbi.com/wp-content/uploads/image5-46.png)

By breaking the data lineage of the *Country* columns, we enabled the successful execution of the [[NATURALLEFTOUTERJOIN]] function. This technique is pivotal when working with non-related tables in DAX, as it allows us to perform joins based on common columns that do not share the same lineage.

## Conclusions

Using [[NATURALLEFTOUTERJOIN]] and [[NATURALINNERJOIN]] functions in DAX on model tables that do not have regular relationships requires using columns without a data lineage. We can obtain these columns by using an expression that breaks the data lineage, making it possible to use columns with the same name as join conditions.

[[CONVERT]]

Convert an expression to the specified data type.

`CONVERT ( <Expression>, <DataType> )`

[[ADDCOLUMNS]]

Returns a table with new columns specified by the DAX expressions.

`ADDCOLUMNS ( <Table>, <Name>, <Expression> [, <Name>, <Expression> [, … ] ] )`

[[NATURALLEFTOUTERJOIN]]

Joins the Left table with right table using the Left Outer Join semantics.

`NATURALLEFTOUTERJOIN ( <LeftTable>, <RightTable> )`

[[NATURALINNERJOIN]]

Joins the Left table with right table using the Inner Join semantics.

`NATURALINNERJOIN ( <LeftTable>, <RightTable> )`

[[SELECTCOLUMNS]]

Returns a table with selected columns from the table and new columns specified by the DAX expressions.

`SELECTCOLUMNS ( <Table> [[, <Name>], <Expression> [[, <Name>], <Expression> [, … ] ] ] )`
