---
title: "Letter case-sensitivity in DAX, Power BI and Analysis Services"
url: "https://www.sqlbi.com/articles/letter-case-sensitivity-in-dax-power-bi-and-analysis-services"
published: "2021-03-11"
updated: 
source: "sqlbi.com"
---

# Letter case-sensitivity in DAX, Power BI and Analysis Services

> 发布：2021-03-11

If you want to start a war, just enter into a room crowded with IT people and ask, “should a programming language be case-sensitive or not?”. Then, enjoy the popcorn you wisely brought with you to the debate. Jokes aside, deciding whether “Paul” equals “PAUL” is a complex matter. Technically, the two names are different. Despite that, many users would claim that they actually represent the same person, therefore they should be considered equal.

Every new language defines its own rules of case-sensitivity. R and Python are case-sensitive, DAX is not. It is not that one is right and the others are not; it is really a matter of personal taste of the author of the language. We would say that there is an equal number of pros and cons in both choices. Therefore, there is no definitive choice. That said, a choice needs to be made on two aspects: the language itself and the way it considers strings. Pascal, for example, is case-insensitive as a language, but string comparison is case-sensitive. The M language, in Power Query, is case-sensitive despite living in the same environment as DAX. DAX is case-insensitive as a formula language. And when it comes to string comparison, you have different options:

- If you use Power BI Desktop, Power BI Service, or Azure Analysis Services, the string comparison is case-insensitive and you have no option to change that.
- If you use SQL Server Analysis Services, then you can choose whether you want a case-sensitive or case-insensitive collation style. By default, you are working with case-insensitive collation.

The choice made by the Tabular designers was – in our opinion – the correct choice. When producing reports, you do not want to discriminate between lowercase and uppercase. Besides, strings in databases may be stored as uppercase for the very reason that you do not want to make any distinction; you thus store everything uppercase.

Tabular does not store everything uppercase. Strings keep their original format: a mixture of lower and upper cases. The casing is ignored when making comparisons. Therefore, “A” equals “a” and “JOHN” equals “John” even though the strings are stored in a different way.

With all that said, when your tables store a mix of lowercase and uppercase strings, you might end up obtaining unexpected results. Knowing this in advance, lets you develop your models the right way. Therefore, from now on we show a set of queries with a mixture of lower and upper case letters. In most cases, the results will be unexpected. Our hope is that by the end of this article you will start recognizing when and how case-sensitivity matters.

Before we start looking at DAX code, we still need to briefly review how Tabular stores a table. Look at the following table definition:

```dax




VAR Names = { "Alberto", "Marco", "Claire", "Daniele", 

              "Daniele", "Claire", "Alberto", "Marco" }



```

Even though we provided a list of names, the engine stores this table in a different way. It builds a dictionary with all the distinct values of the column; it then replaces the names in the table with the position of the name in the dictionary. These are internal structures in the engine. The following code is useful only to understand how the data is actually stored. First, the table, next the two structures:

```dax


VAR Names = { "Alberto", "Marco", "Claire", "Daniele", 

              "Daniele", "Claire", "Alberto", "Marco" }

--

--  This table is actually stored in two data structures

--  with the following content:

--

VAR Names_Dictionary = 

    {

        ( 1, "Alberto" ),

        ( 2, "Marco" ),

        ( 3, "Claire" ),

        ( 4, "Daniele" )

    }

VAR Names_Content = { 1, 2, 3, 4, 4, 3, 1, 2 }

```

Because each name appears twice in the original table, this new structure is more efficient because it replaces long names with integer values. For each value, the engine must check whether it is already present in the dictionary or not. And here is where the issue of case-sensitivity becomes an important topic: What if “Daniele” is present both as “Daniele” and “DANIELE”? Are the two strings equal? By default, in DAX they are.

As a consequence of what we have outlined so far, when values are inserted into a table, two strings that differ only because of the casing are stored with the same index – thus resulting in the same string. You think you are creating a value, the engine stores a different value.

Let us look at a few examples. In the first example, the table contains upper “A”, lower “a” and lower “b”. As you see, the result contains an upper “A” twice, because the lowercase “a” has been replaced with an uppercase “A”.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-7.png)

Why did the engine choose the uppercase “A” and not the lowercase “a”? Simply because the uppercase “A” was encountered first during processing. If we try another table where the first “a” is lowercase, the result is indeed different and results in two lowercase “a”.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-5.png)

As a rule, the first string that is added to the dictionary defines the casing of all subsequent strings that are equal. By “equal” we mean case-insensitive equal. This process happens for any operation regarding tables. For example, this is the result using a set function to produce the [[UNION]] of the two previous tables.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-2.png)

The first “A” encountered is uppercase, the first “b” is lowercase, and the resulting table repeats a sequence of uppercase “A” and lowercase “b”, despite the casing of the second argument of [[UNION]]. You can easily try different combinations of set functions and different orders for the two tables. The result always follows the same pattern: the first instance of a string defines the casing of all the subsequent strings.

Finally, this replacement operation happens only when a value is added to a table. For example, the result of the [[LOWER]] function is a string converted to lowercase. That said, if you add the result of [[LOWER]] to a table where a string with a different casing exists, then the result of [[LOWER]] is replaced with the previously-added string.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-2.png)

This behavior of DAX is not a problem whenever the strings you are handling represent people, products, or human-readable entities. Indeed, it is common sense not to consider the casing when comparing two names. As such, the behavior of DAX is acceptable in most of the scenarios.

If the strings you are treating represent internal references or codes, then the behavior might be problematic. If your data source contains references where case-sensitivity matters for any reason, then you need to pay extra attention to these strings: there is a genuine possibility that the data imported has been mishandled by the Tabular engine.

As we said in the introduction, if you use Power BI Desktop, the Power BI service, or Azure Analysis Services, you have no choice: the instances all use case-insensitive collation. If you are working on your private SQL Server Analysis Services instance, then you can choose the collation style to use during setup. In this latter case, you may want to opt for case-sensitive collation.

Although you could use case-sensitive collation, we advise you not to do it. Instead, find another way to handle the issue – for example by replacing those internal codes with a new integer key. If you rely on the local engine being case-sensitive, you incur the risk that users export data from your model and process it using their Power BI Desktop instance. At that point, the problem would arise anyway, and it would be outside of IT’s control. These data problems need to be solved as early as possible in the data supply chain to avoid serious issues at the end of the chain. Users are not expected to even realize that incorrect results might be linked to case-sensitivity.

[[UNION]]

Returns the union of the tables whose columns match.

`UNION ( <Table>, <Table> [, <Table> [, … ] ] )`

[[LOWER]]

Converts all letters in a text string to lowercase.

`LOWER ( <Text> )`
