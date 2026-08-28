---
title: "Customizing default values for each user in Power BI reports"
url: "https://www.sqlbi.com/articles/customizing-default-values-for-each-user-in-power-bi-reports"
published: "2021-07-19"
updated: 
source: "sqlbi.com"
---

# Customizing default values for each user in Power BI reports

> 发布：2021-07-19

Tabular offers the built-in feature of hiding rows of data from specific users. For example, you can create a set of security rules to let a store manager see only the sales of their store. This works fine if your goal is to secure data, which means preventing access to data that a user is not expected to see.

Another common requirement is to be able to select by default, for a store manager, their sales. With that said, store managers can see the data of other stores, but they need to explicitly request it. In other words: by default, the store manager sees the sales of their store only. By using a slicer, they can choose a different combination of stores.

Let us elaborate a bit more with an example. We want to define these rules for the defaults:

![](https://cdn.sqlbi.com/wp-content/uploads/image1-15.png)

By default, Alberto sees the data for stores in China and sales in 2008. Marco sees Germany and the United States in both 2008 and 2009. When either Marco or Alberto browse the report, they can choose different combinations. Nonetheless, without an explicit choice the report shows the default data. For example, below is what Marco would see when browsing the Sales report.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-13.png)

The red label is showing what the selection is. Indeed, the slicers on *Calendar Year* and *CountryRegion* are not filtering anything. There are three countries in *Store[CountryRegion]* with sales, but the report is showing only two countries: Germany and the United States, which are the defaults for Marco. The year works in a similar way: there are many years with data, but because the slicer is not filtering anything the report shows only 2008 and 2009.

The label indicating what the user is seeing is extremely important in reports that use default values. Without the red label, users would have no clue about the fact that they are seeing only a portion of the data. If you plan on providing defaults, plan explanation labels to go along.

Tabular does not come with the feature to assign a default filter to a column. Therefore, we use a workaround based on calculation groups. A calculation group can be used to apply a filter to the report in an overall way, thus affecting all the calculations.

The solution requires a calculation group; when activated, that calculation group checks whether the report is actively filtering the columns for which we want a default value – country and year in our scenario. If there is a filter, then the calculation group should not do anything and let the base flow of computation proceed. Otherwise, if there are no selected values the calculation group applies a filter to the proper columns using the default values for the user.

Depending on the number of columns for which you want a default value, the code can be rather simple or quite complex. Actually, the solution is simple only in the very special case when you want to provide a default value for one column only. As soon as you have two or more columns, the code – though not that intricate – requires much higher attention to detail.

## Default value for only one column

Let us start with the simplest scenario. We choose to use a default value for only the *Store[CountryRegion]* column. The table containing the default values is *DefaultCountry*.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-9.png)

We created a M:M cardinality relationship between *DefaultCountry* and *Store*, with the cross-filter direction that goes from *DefaultCountries* to *Store*. When *DefaultCountries* is filtered, its filter is transferred to *Store* and from there to the entire model.

Before moving further, let us focus on a small detail. That detail is easy to grasp at this point, whereas it would be harder later: when *DefaultCountry* is being filtered, so is *Store*. If *DefaultCountry* is not filtered, then *Store* is not filtered either. If *DefaultCountry* is filtered, *Store* shows only the *Country* values that are visible in *DefaultCountry*. Read this small paragraph a couple of times, until you find it obvious. It is indeed obvious, but trust us, this is a small detail that will become extremely important later. That is the reason the code will suddenly become more complex with two or more default columns.

The *OnlyCountry* calculation item performs three actions. First, it checks whether there is an explicit filter on the *Store[CountryRegion]* column. If there are any filters, then it just computes [[SELECTEDMEASURE]] with no further actions. If no filters are present, then it uses [[USERPRINCIPALNAME]] to retrieve the user currently browsing the model and uses it to apply a filter to *DefaultCountries*. *DefaultCountries* filters *Store* automatically because of the relationship:

```dax


--

--  If there is only one column used for the default value, then

--  it is enough to apply the filter on the default value table and filter

--  it with USERPRINCIPALNAME. The filter propagates to the related table and applies

--  the default filtering.

--

IF (

    CALCULATE (

        NOT ISCROSSFILTERED ( Store[CountryRegion] ),

        ALLSELECTED ()

    ),

    CALCULATE (

        SELECTEDMEASURE (),

        DefaultCountry[User] = USERPRINCIPALNAME ()

    ),

    SELECTEDMEASURE ()

)

```

By using the *OnlyCountry* calculation item, you see that the report shows larger values for the *Sales Amount* measure; what is more, the red label highlights that all years are visible. The reason is that this first calculation item only applies the default to the *Store[Country]* column, showing all the years.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-9.png)

## Default value for multiple columns

The solution outlined so far is neat and simple. Its major drawback is that it works only when you have one column for the default values. However, this simple solution cannot work with multiple columns.

First, we added the *DefaultYear* table to the data model to provide a default filter for the year.

![](https://cdn.sqlbi.com/wp-content/uploads/image5-6.png)

In the next report we use the default value for the *Store[CountryRegion]* column, whereas we are using 2007 for the year. Indeed, our user explicitly asked for a year.

![](https://cdn.sqlbi.com/wp-content/uploads/image6-5.png)

In order to compute this report, the *Date[Calendar Year]* column has a filter that is restricting only 2007. We chose 2007 for this example because 2007 does not appear anywhere in the *DefaultYear* table that we use to provide the defaults to the years. In other words, if the *DefaultYear* table were filtered, there is no way 2007 would ever be visible. Remember the statement we highlighted earlier: a filter on *DefaultYear* is propagated to *Date*. If *DefaultYear* is filtered, so is *Date*. If you place a filter on *DefaultYear*, any year not present in *DefaultYear* will not be visible in *Date*.

Therefore, if one of the tables with default values must be filtered, it cannot be part of the [[CALCULATE]] statement and the code must take this into account.

A first solution – that makes sense when you have only two columns – is based on a calculation item named YES that activates the default filtering only on the selected *Year* and/or *Country* attributes:

```dax


-- 

-- Calculation Item: YES

-- 

--

--  With two columns to filter, we need to check which columns need to be filtered

--  and which ones to avoid.

--  This is the reason for the SWITCH statement

--

VAR YearsAlreadyFiltered =

    CALCULATE ( ISCROSSFILTERED ( 'Date'[Calendar Year] ), ALLSELECTED () )

VAR CountriesAlreadyFiltered =

    CALCULATE ( ISCROSSFILTERED ( Store[CountryRegion] ), ALLSELECTED () )

VAR Result =

    SWITCH (

        TRUE,

        NOT ( YearsAlreadyFiltered ) && NOT ( CountriesAlreadyFiltered ),

            CALCULATE (

                SELECTEDMEASURE (),

                DefaultYear[User] = USERPRINCIPALNAME (),

                DefaultCountry[User] = USERPRINCIPALNAME ()

            ),

        NOT YearsAlreadyFiltered, 

            CALCULATE ( 

                SELECTEDMEASURE (), 

                DefaultYear[User] = USERPRINCIPALNAME () 

            ),

        NOT CountriesAlreadyFiltered, 

            CALCULATE ( 

                SELECTEDMEASURE (), 

                DefaultCountry[User] = USERPRINCIPALNAME () 

        ),

        SELECTEDMEASURE ()

    )

RETURN

    Result



```

This solution works with two columns. The code checks which tables must be filtered and executes [[CALCULATE]] with that table only. If both tables need a filter because the user selected neither years nor countries, then [[CALCULATE]] filters both columns with the default values of the current user.

Clearly, by adding more default columns this solution would no longer work, because the number of combinations quickly becomes unmanageable. In order to obtain a generic version that works on any number of columns, we should change our perspective. We can no longer use filters on the *Default-prefixed* tables and trust that they will propagate through the model. The reason is that we now have multiple tables: some tables might require a filter, while other tables must be left unfiltered.

There is no way to choose whether to apply a filter or not to a table in a single [[CALCULATE]] function in DAX. You can select the rows that will be filtered, but you cannot choose whether to apply the filter or not. In the most generic scenario, we have multiple tables that may or may not be filtered. In the previous solution, we solved the problem by using different branches of [[SWITCH]]: in some we filtered both tables, in others we filtered only one. If we have more than two tables, we cannot rely on [[SWITCH]] anymore, because the number of branches would grow exponentially.

This means that we need to use a single [[CALCULATE]] statement that is controlled by variables for the filters. We place the filters that we want to apply into variables, and then we apply them all in a single [[CALCULATE]]. We might want to store the entire table ([[ALL]]) in the variables for the tables that we want not to filter. The problem is that if you place the result of *[[ALL]] ( DefaultYear )* in a variable and use that variable as a filter over the *Date* table, 2007 will not be visible because it is not included in the default filters of any customer. In other words, filtering all the rows of a table is very different from not filtering the table.

The consequence is that in the presence of multiple default columns, the code can no longer rely on a filter on the default tables. Instead, it must compute the set of visible values on the original model tables, and then apply the filter there. As such, it is more complex.

We created a calculation item named YES-Generic that activates the default filtering on both the *Year* and *Country* attributes. Keep in mind that the relationships between the default tables and the model tables are not used with this approach. We left the relationships in the model to test the previous calculation item for two default columns, but they can be removed if you want to implement this second technique.

Here is the code:

```dax


-- 

-- Calculation Item: YES-Generic

-- 

--

--  Check if the years are filtered. If so, we do NOT need to apply the default

--  values. Therefore, we use the value later in YearsToUse. DefaultYears contains

--  the default values, while YearsToUse contains the years to apply as a filter: 

--  either the default ones or the selected ones.

--

VAR YearsAlreadyFiltered =

    CALCULATE ( ISCROSSFILTERED ( 'Date'[Calendar Year] ), ALLSELECTED () )

VAR UserDefaultYears =

    CALCULATETABLE (

        VALUES ( 'DefaultYear'[Calendar Year] ),

        'DefaultYear'[User] = USERPRINCIPALNAME ()

    )

VAR YearsToUse =

    FILTER (

        VALUES ( 'Date'[Calendar Year] ),

        YearsAlreadyFiltered || 'Date'[Calendar Year] IN UserDefaultYears

    )



--

--  Same process used for the years, now replicated for the countries

--

VAR CountriesAlreadyFiltered =

    CALCULATE ( ISCROSSFILTERED ( Store[CountryRegion] ), ALLSELECTED () )

VAR UserDefaultCountries =

    CALCULATETABLE (

        VALUES ( DefaultCountry[Country] ),

        DefaultCountry[User] = USERPRINCIPALNAME ()

    )

VAR CountriesToUse =

    FILTER (

        VALUES ( Store[CountryRegion] ),

        CountriesAlreadyFiltered || Store[CountryRegion] IN UserDefaultCountries

    )

--

--  Now we apply the filters computed earlier to the filter context before calling

--  the selected measure.

--

VAR Result =

    CALCULATE ( 

        SELECTEDMEASURE (), 

        YearsToUse, 

        CountriesToUse

    )

RETURN

    Result



```

Despite being long, the code is quite simple. Focus on the first three variables: *YearsAlreadyFiltered* checks whether there is an active filter on the *Date[Calendar Year]* column. If this is the case, then all the visible values in *Date[Calendar Year]* must be considered. Otherwise, if no filter is present, then we use the values in the *DefaultYear* table. The *UserDefaultYears* variable retrieves the default values; the *YearsToUse* variable stores the *Date[Calendar Year]* values that must be present in the filter. If *YearsAlreadyFiltered* is true, all the values in *YearsToUse* will pass the filter. If *YearsAlreadyFiltered* is false, only the years present in *UserDefaultYears* will pass the filter.

In the end, *YearsToUse* is a table that filters *Date[Calendar Year]* containing the years we want to show.

The next code block – including the variables *ContriesAlreadyFiltered*, *UserDefaultCountries*, and *CountriesToUse* – executes the very same logic on the *Store[Country]* column. Finally, we compute in *Result* the [[SELECTEDMEASURE]] applying the filters as needed.

As you see, in *Result* both columns are always filtered. There is no way you can avoid one of the two filters, and if the filter were on the *Default-prefixed* tables, that would break the mechanism.

With the calculation item working, we can now take a quick look at the *Default Label* measure used to show the visible values in the red label of the report:

```dax


Default Label := 

--

--  This measure returns a textual description of the items filtered, either

--  because of an explicit filter or because they are used as default.

--

VAR Countries =

    IF (

        ISCROSSFILTERED ( Store[CountryRegion] ),

        "countries: "

            & CONCATENATEX (

                VALUES ( Store[CountryRegion] ),

                Store[CountryRegion],

                ", "

            ),

        "ALL countries"

    )

VAR Years =

    IF (

        ISCROSSFILTERED ( 'Date'[Calendar Year] ),

        "years: "

            & CONCATENATEX (

                VALUES ( 'Date'[Calendar Year] ),

                'Date'[Calendar Year],

                ", "

            ),

        "ALL years"

    )

VAR Result = "Showing " & Countries & " and " & Years

RETURN

    Result



```

The measure checks if there are filters on either the *Store[CountryRegion]* column or on the *Date[Calendar Year]* column. If there is a filter, it builds a string containing the visible values separated by a comma.

Last, a few words about performance. Among the different solutions proposed, the only one with a low impact from the performance point of view is the first one, which works when you have a default for only one column. The version with two columns is slower and more complex, and the most generic version contains a lot of code that requires the formula engine to kick in heavily. Besides, even though we showed a generic version with only two columns, you can easily extend it to handle more defaults. It will however turn out to be even slower. Therefore, expect a strong impact on performance.

## Conclusion

The requirement to have default values for columns is very common, and we certainly hope that the feature will be added as a model property at some point. Before then, you can build a solution by mixing calculation groups, DAX code and attention to small details, thus providing different defaults to different users.

[[SELECTEDMEASURE]]

Context transition

Returns the measure that is currently being evaluated.

`SELECTEDMEASURE ( )`

[[USERPRINCIPALNAME]]

Returns the user principal name.

`USERPRINCIPALNAME ( )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`

[[SWITCH]]

Returns different results depending on the value of an expression.

`SWITCH ( <Expression>, <Value>, <Result> [, <Value>, <Result> [, … ] ] [, <Else>] )`

[[ALL]]

CALCULATE modifier

Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied.

`ALL ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`
