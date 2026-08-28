---
title: "NATURALLEFTOUTERJOIN"
function: "naturalleftouterjoin"
category: "Table manipulation"
url: "https://dax.guide/naturalleftouterjoin/"
source: "dax.guide"
重要度:
难度:
---

# NATURALLEFTOUTERJOIN DAX Function (Table manipulation)

Joins the Left table with right table using the Left Outer Join semantics.

## Syntax

NATURALLEFTOUTERJOIN ( <LeftTable>, <RightTable> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| LeftTable |  | The Left-side table expression to be used for join. |
| RightTable |  | The Right-side table expression to be used for join. |

## Return values

Table An entire table or a table with one or more columns.

A table which includes only rows from RightTable for which the values in the common columns specified are also present in LeftTable. The table returned will have the common columns from the left table and the other columns from both the tables.

## Remarks

There is no sort order guarantee for the results.  
Columns being joined must have the same data type and the same name in both tables.  
The columns considered for the join are those of the expanded table, not just the base table: two tables can be joined through common columns in related tables.  
The columns used in the join condition that correspond to physical columns of the data model must also have the same data lineage; two columns with the same name and different data lineage generate an error.  
Two columns with the same data lineage must have also the same full column name, which includes both table name and column name; otherwise, they are not matched for the join.  
Strict comparison semantics are used during the join. There is no type coercion; for example, 1 does not equal 1.0.

[» 5 related articles](#articles)  

## Examples

```dax


--  NATURALLEFTOUTERJOIN performs a left outer join between two

--  tables, joining columns with the same name. 

--  NATURALINNERJOIN performs an inner join.

--  Corresponding columns must both have the same lineage, or no lineage.

DEFINE

    VAR StoresByCountry = 

        SELECTCOLUMNS (

            TREATAS ( { "Armenia", "Australia", "Denmark" }, Store[CountryRegion] ),

            "Country", Store[CountryRegion] & "",

            "NumOfStores", CALCULATE ( COUNTROWS ( Store ) )

        )

    VAR CustomersByCountry = 

        SELECTCOLUMNS (

            TREATAS ( { "Armenia", "Australia", "Denmark" }, Customer[CountryRegion] ),

            "Country", Customer[CountryRegion] & "",

            "NumOfCustomer", CALCULATE ( COUNTROWS ( Customer ) )

        )

        

EVALUATE StoresByCountry



EVALUATE CustomersByCountry



EVALUATE

    NATURALLEFTOUTERJOIN ( StoresByCountry, CustomersByCountry )

    

EVALUATE

    NATURALLEFTOUTERJOIN ( CustomersByCountry, StoresByCountry )

    

EVALUATE

    NATURALINNERJOIN ( StoresByCountry, CustomersByCountry )



```

| Country | NumOfStores |
| --- | --- |
| Armenia | 1 |
| Australia | 3 |
| Denmark | 1 |

| Country | NumOfCustomer |
| --- | --- |
| Armenia | 1 |
| Australia | 3,631 |

| Country | NumOfStores | NumOfCustomer |
| --- | --- | --- |
| Armenia | 1 | 1 |
| Australia | 3 | 3,631 |
| Denmark | 1 | (Blank) |

| Country | NumOfCustomer | NumOfStores |
| --- | --- | --- |
| Armenia | 1 | 1 |
| Australia | 3,631 | 3 |

| Country | NumOfStores | NumOfCustomer |
| --- | --- | --- |
| Armenia | 1 | 1 |
| Australia | 3 | 3,631 |

## Related articles

Learn more about NATURALLEFTOUTERJOIN in the following articles:

- [**From SQL to DAX: Joining Tables**](https://www.sqlbi.com/articles/from-sql-to-dax-joining-tables/)

  In SQL there are different types of JOIN, available for different purposes. This article shows the equivalent syntaxes supported in DAX and it was updated in May 2018. [» Read more](https://www.sqlbi.com/articles/from-sql-to-dax-joining-tables/)
- [**Lookup multiple values in DAX**](https://www.sqlbi.com/articles/lookup-multiple-values-in-dax/)

  This article describes different techniques to retrieve multiple values from a lookup table in DAX, improving code readability and performance. [» Read more](https://www.sqlbi.com/articles/lookup-multiple-values-in-dax/)
- [**Replacing relationships with join functions in DAX**](https://www.sqlbi.com/articles/replacing-relationships-with-join-functions-in-dax/)

  This article describes how to join tables in DAX when there are no relationships in the data model. The data lineage plays an essential role in this scenario. [» Read more](https://www.sqlbi.com/articles/replacing-relationships-with-join-functions-in-dax/)
- [**Using join functions in DAX**](https://www.sqlbi.com/articles/using-join-functions-in-dax/)

  This article describes the practical uses of NATURALLEFTOUTERJOIN and NATURALINNERJOIN in DAX. These functions are not commonly used in DAX because they do not have the same flexibility as the corresponding concepts in SQL. [» Read more](https://www.sqlbi.com/articles/using-join-functions-in-dax/)
- [**Account receivable aging in Power BI**](https://www.sqlbi.com/articles/account-receivable-aging-in-power-bi/)

  This article describes an Accounts Receivable Aging report in Power BI, and shows how to simplify a business problem using existing modeling patterns. [» Read more](https://www.sqlbi.com/articles/account-receivable-aging-in-power-bi/)

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/naturalleftouterjoin-function-dax](https://docs.microsoft.com/en-us/dax/naturalleftouterjoin-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
