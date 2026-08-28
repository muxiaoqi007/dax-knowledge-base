---
title: "Comparing different school terms in Power BI"
url: "https://www.sqlbi.com/articles/comparing-different-school-terms-in-power-bi"
published: "2023-07-31"
updated: 
source: "sqlbi.com"
---

# Comparing different school terms in Power BI

> 发布：2023-07-31

There are many businesses where a comparison over time is needed using time periods that do not match the common way to group days, such as weeks, months, quarters, and years. For example, many countries split the academic calendar into terms, and several industries use seasons. In this article, we use the example of school terms, but you can use the same approach in other scenarios.

## Addressing the business requirements

A requirement to apply the following technique is that every day can belong to a term or not, but there are no overlaps between terms. Indeed, if we had overlapping periods, we should create a different solution based on the [Comparing different time periods pattern](https://www.daxpatterns.com/comparing-different-time-periods/). In the case of school terms, we consider the case of three terms per year, where the first term starts in September of one year, and the last term ends in July of the following year. Therefore, the academic year is identified by two consecutive numbers, such as 2016-2017 (often shortened to 2016-17).

The business requirement is to compare one term with the previous term (within the same academic year or the previous one when we compare the first term of a year) and one term with the same term in the previous year. The goal is to obtain a result similar to the following one.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-60.png)

For this example, we are using the sales of our Contoso sample database, but we expect that the measures used in this scenario will count for example the number of students or their . However, this does not change the modeling requirements: after all, why not compare sales by school terms?

The starting point is the classic star schema of Contoso.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-57.png)

We add the *Terms* table, which defines each term’s start and end date.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-50.png)

The *Terms[Term]* column is sorted by *Terms[Term Number]*. Each year has three terms: Autumn, Spring, and Summer. The *Terms* table can populate additional columns in *Date*, but we want to add an important column before doing that. To compare terms, it is easier to use a sequential index for all the terms in all the years rather than manage the filter on two columns, such as *Year* and *Term Number*. Therefore, we create the *TermSequentialNumber* calculated column to satisfy both requirements:

Calculated column in Terms table

```dax


TermSequentialNumber = 

LEFT ( Terms[Year], 4 ) * 3 + Terms[Term Number]

```

We prefer a formula that can produce the same result for the same year and term rather than a solution based on [[RANKX]] starting with one from the first term available. This way, any filter saved on a report will preserve its state even if the content of the *Terms* table changes in the future. The *TermsSequentialNumber* column should be hidden, as it provides no meaningful information to report users.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-45.png)

The *TermSequentialNumber* column is also useful for creating a regular many-to-one relationship between *Date* and *Terms*, as we will see later in this article.

## Extending the *Date* table

The *Terms* table can be exposed directly to the user, or it can be used to extend the *Date* table with additional columns. We start with the latter scenario, where we copy into *Date* the *Term*, *Term Number*, *Year*, and *TermSequentialNumber* columns from the *Terms* table. To retrieve each column, we use the pattern described here for the *Term* column:

Calculated column in Date table

```dax


Term = 

SELECTCOLUMNS (

    FILTER (

        Terms,

        Terms[From Date] <= 'Date'[Date] 

            && Terms[To Date] >= 'Date'[Date]

    ),

    "Term", Terms[Term]

)

```

Because the *Year* column already exists in *Date*, we store it in a *Term* *Year* column:

Calculated column in Date table

```dax


Term Year = 

SELECTCOLUMNS (

    FILTER (

        Terms,

        Terms[From Date] <= 'Date'[Date] 

            && Terms[To Date] >= 'Date'[Date]

    ),

    "Term Year", Terms[Year]

) 

```

If the *Terms* table has overlapping periods, the calculated columns fail with an error and stop the refresh operation. This error is by design: we prefer not to show incorrect information in the report. You can provide a better diagnostic to the system administrator by customizing the error message in the case of overlapping ranges:

Calculated column in Date table

```dax


TermSequentialNumber = 

IFERROR (       

    SELECTCOLUMNS (

        FILTER (

            Terms,

            Terms[From Date] <= 'Date'[Date] 

                && Terms[To Date] >= 'Date'[Date]

        ),

        "id", Terms[TermSequentialNumber]

    ),

    ERROR ( "Check overlapping dates in Terms table" )

)

```

The [[ERROR]] function in [[IFERROR]] converts a generic error caused by an overlapping range of dates in the *Terms* table (“A table of multiple values was supplied where a single value was expected”) into a message that makes more sense, which is logged as the reason why the refresh operation fails.

We keep only the *Term* and *Term Year* columns visible, whereas we hide *Term Number* and *TermSequentialNumber*. We also set the *Term* column to be sorted by *Term Number*. Eventually, the dates that do not belong to any term show a blank in these new columns.

The final step is to create measures that implement the desired comparison: *Previous Term* and *Same Term Previous Year*. We use the same approach for both measures: identify the term selected by reading the corresponding *TermSequentialNumber* value and move the filter context to the desired term by filtering the same *TermSequentialNumber* column. For *Previous Term*, we just move the filter to the previous value of *TermSequentialNumber*:

Measure in Sales table

```dax


Previous Term = 

VAR CurrentTerm = SELECTEDVALUE ( 'Date'[TermSequentialNumber] )

VAR PreviousTerm = CurrentTerm - 1

RETURN 

    IF (

        NOT ISBLANK ( CurrentTerm ) && PreviousTerm > 0,

        CALCULATE ( 

            [Sales Amount],

            REMOVEFILTERS ( 'Date' ),

            'Date'[TermSequentialNumber] = PreviousTerm

        )

    )

```

The code of *Previous Term* returns blank if there is more than one term selected or if the previous term is not identified correctly. Indeed, the *PreviousTerm* variable computes the target value of *TermSequentialNumber* by subtracting one from the value obtained with [[SELECTEDVALUE]]. This way, the term before the Autumn of an academic calendar is the Summer of the previous academic calendar, without requiring a special DAX code to handle the particular condition of the first term of one year that must filter the last term of the previous year.

The code for *Same Term* *Previous Year* is almost identical to that of *Previous Term*: the only difference is that instead of subtracting one term, the *PreviousTerm* variable subtracts three terms from the value in *CurrentTerm*, because there are three terms in each year. If you use a different configuration, the subtracted number should correspond to the number of terms in one year.

Measure in Sales table

```dax


Same Term Previous Year = 

VAR CurrentTerm = SELECTEDVALUE ( Terms[TermSequentialNumber] )

VAR PreviousTerm = CurrentTerm - 3

RETURN 

    IF (

        NOT ISBLANK ( CurrentTerm ) && PreviousTerm > 0,

        CALCULATE ( 

            [Sales Amount],

            REMOVEFILTERS ( 'Date' ),

            Terms[TermSequentialNumber] = PreviousTerm

        )

    )

```

The presence of [[REMOVEFILTERS]] in both measures is required to remove the existing filter context on *Date*. In case the *Date* column had filters to preserve, such as *Day of Week* or *Working Day*, then you can use [[ALLEXCEPT]] instead of [[REMOVEFILTERS]], keeping the filter on all the columns that should preserve their state:

Measure in Sales table

```dax


Previous Term Preserve Days = 

VAR CurrentTerm = SELECTEDVALUE ( 'Date'[TermSequentialNumber] )

VAR PreviousTerm = CurrentTerm - 1

RETURN 

    IF (

        NOT ISBLANK ( CurrentTerm ) && PreviousTerm > 0,

        CALCULATE ( 

            [Sales Amount],

            ALLEXCEPT( 

                'Date',

                'Date'[Day of Week Number],

                'Date'[Day of Week Short],

                'Date'[Day of Week],

                'Date'[Working Day]

            ),

            'Date'[TermSequentialNumber] = PreviousTerm

        )

    )

```

This latter technique is compatible with a slicer on *Day of Week*, as in the following example.

![](https://cdn.sqlbi.com/wp-content/uploads/image5-38.png)

## Connecting *Terms* to *Date*

Instead of extending the *Date* table, we can make the *Terms* table visible and connect it to *Date*. To achieve this, we must only create the *TermSequentialNumber* hidden column in the *Date* table, as we have already seen in the previous section:

Calculated column in Date table

```dax


TermSequentialNumber = 

IFERROR (       

    SELECTCOLUMNS (

        FILTER (

            Terms,

            Terms[From Date] <= 'Date'[Date] 

                && Terms[To Date] >= 'Date'[Date]

        ),

        "id", Terms[TermSequentialNumber]

    ),

    ERROR ( "Check overlapping dates in Terms table" )

)

```

We refrain from using [[CALCULATE]] and a context transition in a column we want to use in a relationship to avoid possible circular dependencies – solving that would require a much longer DAX expression.

Once we have the *TermSequentialNumber* columns, we create the relationship between *Terms* and *Date*.

![](https://cdn.sqlbi.com/wp-content/uploads/image6-33.png)

Defining the relationship is an effective way to reuse existing columns in *Terms* such as *Year* and *Term*. The only difference in the measures is that we use the *TermSequentialNumber* column from *Terms* instead of *Date*, as in the following example:

Measure in Sales table

```dax


Previous Term = 

VAR CurrentTerm = SELECTEDVALUE ( Terms[TermSequentialNumber] )

VAR PreviousTerm = CurrentTerm - 1

RETURN 

    IF (

        NOT ISBLANK ( CurrentTerm ) && PreviousTerm > 0,

        CALCULATE ( 

            [Sales Amount],

            REMOVEFILTERS ( 'Date' ),

            Terms[TermSequentialNumber] = PreviousTerm

        )

    )

```

The [[REMOVEFILTERS]] applied on *Date* removes the existing filter context on both *Date* and *Terms*, because the latter is part of the *Date* [expanded table](https://www.sqlbi.com/articles/expanded-tables-in-dax/) and does not require an additional [[REMOVEFILTERS]]. In case the *Date* column has filters to preserve, such as *Day of Week* or *Working Day*, then you can use [[ALLEXCEPT]] instead of [[REMOVEFILTERS]]; this is because the filter removal on *Date* still applies to the expanded table, as we have already seen in the *Previous Term Preserve Days* measure.

## Conclusions

Creating arbitrary time periods in the *Date* table requires additional columns to simplify the DAX code required to compare different periods – and this results in better performance than solutions where DAX analyzes the range of each period at query time dynamically. The more important column is a sequential index that uniquely identifies each time period. The technique described in this article is a specific example of the more generic technique described in [custom time-related calculations](https://www.daxpatterns.com/custom-time-related-calculations/) in DAX Patterns.

[[RANKX]]

Returns the rank of an expression evaluated in the current context in the list of values for the expression evaluated for each row in the specified table.

`RANKX ( <Table>, <Expression> [, <Value>] [, <Order>] [, <Ties>] )`

[[ERROR]]

Raises a user specified error.

`ERROR ( <ErrorText> )`

[[IFERROR]]

Returns value\_if\_error if the first expression is an error and the value of the expression itself otherwise.

`IFERROR ( <Value>, <ValueIfError> )`

[[SELECTEDVALUE]]

Returns the value when there’s only one value in the specified column, otherwise returns the alternate result.

`SELECTEDVALUE ( <ColumnName> [, <AlternateResult>] )`

[[REMOVEFILTERS]]

CALCULATE modifier

Clear filters from the specified tables or columns.

`REMOVEFILTERS ( [<TableNameOrColumnName>] [, <ColumnName> [, <ColumnName> [, … ] ] ] )`

[[ALLEXCEPT]]

CALCULATE modifier

Returns all the rows in a table except for those rows that are affected by the specified column filters.

`ALLEXCEPT ( <TableName>, <ColumnName> [, <ColumnName> [, … ] ] )`

[[CALCULATE]]

Context transition

Evaluates an expression in a context modified by filters.

`CALCULATE ( <Expression> [, <Filter> [, <Filter> [, … ] ] ] )`
