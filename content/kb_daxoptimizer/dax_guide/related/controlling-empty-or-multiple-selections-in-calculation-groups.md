---
title: "Controlling empty or multiple selections in calculation groups"
url: "https://www.sqlbi.com/articles/controlling-empty-or-multiple-selections-in-calculation-groups"
published: "2025-06-02"
updated: 
source: "sqlbi.com"
---

# Controlling empty or multiple selections in calculation groups

> 发布：2025-06-02

Calculation groups are often used to display options in a report to change the calculation of existing measures by selecting items on a slicer. However, only a single calculation item can be executed for a measure reference, which could make the semantic model harder to use when the user selects two or more items in a calculation group.

Two new calculation group properties, *multipleOrEmptySelectionExpression* and *noSelectionExpression*, provide a way to control the calculation in these conditions that, so far, ignored the presence of the calculation group, thus executing the measures without applying any transformation. This article shows how to use these features and provides guidance on using the feature in preview: despite not having a user interface to manage these new properties, the TMDL view in Power BI Desktop and external tools like Tabular Editor already allow you to create and publish a semantic model that uses these new properties.

## Introducing multiple selections in calculation groups

A calculation group shows to the users possible variations of existing measures based on a selection of an item from a list, like when you choose the months to display in a slicer. However, calculation groups have been designed to execute the DAX code of only one calculation item selected. As long as the calculation items are displayed in a Power BI visual, a multiple selection in a slicer over the calculation group does not create any issue, because every cell in the visual only has one calculation item applied. We see this in the following example where the Metric slicer shows the value of the selected measure with the original value (Original), divided by 1,000 (K), or divided by 1,000,000 (M).

![](https://cdn.sqlbi.com/wp-content/uploads/image1-108.png)

However, what happens when two calculation items are selected from the same calculation group and the value is displayed in a cell? Or what happens when no items are selected at all in a slicer? From the point of view of the measure evaluation, because multiple calculation items are active simultaneously, no code of the calculation group is evaluated in either case. For example, when the calculation group column is not included in the visual, a single selection in the Metric slicer applies the selected calculation item to each measure being displayed.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-105.png)

The *Margin %* measure shows the unaltered, original value because the activated calculation item does not affect measures with a “%” in the name:

Calculation Group in Unit measure A table

```dax


-- Item

K =

IF ( 

    NOT CONTAINSSTRING ( SELECTEDMEASURENAME(), "%" ),

    SELECTEDMEASURE() / 1000,

    SELECTEDMEASURE()

)



-- Format String

IF ( 

    NOT CONTAINSSTRING ( SELECTEDMEASURENAME(), "%" ),

    "#,0.00k",

    SELECTEDMEASUREFORMATSTRING()

)

```

However, if multiple calculation items are selected in the slicer, none of them are applied to the measures of the visual. If a single cell of the visual has more than one calculation item active, they are ignored, and the measure is evaluated without any transformation.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-95.png)

This is the same as the behavior we observe when no calculation item is selected. In this case too, the original measures are evaluated without any transformation.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-89.png)

You may not like this behavior, especially when multiple calculation items are selected: you may want to force a selection or at least show that an invalid combination has been chosen. In any case, you would like to intercept the measure evaluation in these last two examples to control the result displayed in the report.

## Intercepting multiple selections in calculation groups

The Calculation Group object in the semantic model has two properties to manage multiple selections, and no selection. As of May 2025, these properties are in preview. Because of their preview state, Power BI Desktop supports them only through the TMDL view, but you can also manipulate them using an external editor such as [Tabular Editor](https://www.sqlbi.com/tools/tabular-editor/).

We implement a custom DAX expression when more than one calculation item is selected in the Metric slicer. Our first implementation shows no valid values, and raises an error with a custom message saying, “Multiple selection of Metric is not allowed”. The **multipleOrEmptySelectionExpression** property in the calculationGroup object can be assigned to the DAX expression, which must be executed when two or more calculation items are selected. We can assign that property by writing the highlighted code in the calculation group object displayed in the TMDL view.

![](https://cdn.sqlbi.com/wp-content/uploads/image5-79.png)

If you are new to TMDL, here are the steps to follow:

1. Open the TMDL view
2. Create the script for the calculation group
   1. Right-click the calculation group in the Model pane
   2. Click “Script TMDL to”
   3. Click “Script tab”

   ![](https://cdn.sqlbi.com/wp-content/uploads/image6-72.png)
3. Write the following expression within the calculationGroup definition:

```dax


multipleOrEmptySelectionExpression = ERROR ( "Multiple selection of Metric is not allowed" )

```

4. Click APPLY to update the semantic model. At this point, you may see a “Compatibility Level Upgrade” dialog box that asks you to increase the compatibility level of the semantic model: in that case, click “Yes”.

![](https://cdn.sqlbi.com/wp-content/uploads/image7-61.png)

The “Compatibility Level Upgrade” dialog box appears only when the model compatibility level is less than 1605, the minimum required for these calculation group properties. It is important to know that, if you are using Tabular Editor, it does not show *multipleOrEmptySelectionExpression* nor other calculation properties when the compatibility level is less than 1605. In that case, you must upgrade the compatibility level to 1605 (you can do this in Tabular Editor), and you can then edit all the calculation group properties.

If two or more calculation items are selected, we no longer see the contents of the visual; we see an error with the message we defined in the [[ERROR]] function, which is highlighted in the following screenshot.

![](https://cdn.sqlbi.com/wp-content/uploads/image8-50.png)

Raising an error was the easiest way to demonstrate this functionality. However, you may want to apply a different logic, for example by choosing one of the items selected based on a prioritization you define. To illustrate, the following implementation chooses the calculation item with the highest ordinal value, the last of the selected values in the slicers. As you can see in the following TMDL snippet code, the *formatStringDefinition* property defines the behavior of the dynamic format string when there is a multiple selection:

```dax


calculationGroup

    multipleOrEmptySelectionExpression = ```

            VAR maxSelection = MAX ( 'Unit measure C'[Ordinal] )

            VAR selection = 

                LOOKUPVALUE ( 

                    'Unit measure C'[Metric], 

                    'Unit measure C'[Ordinal], maxSelection 

                )

            RETURN CALCULATE ( 

                SELECTEDMEASURE(), 

                'Unit measure C'[Metric] = selection

            )

            ```

            

        formatStringDefinition = ```

            VAR maxSelection = MAX ( 'Unit measure C'[Ordinal] )

            VAR selection = 

                LOOKUPVALUE ( 

                    'Unit measure C'[Metric], 

                    'Unit measure C'[Ordinal], maxSelection 

                )

            RETURN CALCULATE ( 

                SELECTEDMEASUREFORMATSTRING(), 

                'Unit measure C'[Metric] = selection

            )

            ```

```

Conceptually, the DAX code we wrote intercepts the measure execution when more than one calculation item is visible in the filter context. The DAX code we wrote operates within the current filter context. We can inspect the selection by looking at the values visible in the filter context for the *Metric* column, as well as for the *Ordinal* column we used to respect the order of the calculation items in the model – this is regardless of their alphabetical order, which is what we would have done if we used [[MAX]] over the *Metric* column instead of over the *Ordinal* column. You can write more complex code to handle similar situations. Just keep in mind that a calculation group is a table in the semantic model, and the calculation item is a value in a column of this table.

A more complex operation could be to apply all the selected calculation items. The complexity here is related to the absence of recursion in DAX: we cannot get a list of calculation items to execute and apply each calculation item to the result of the previous calculation item. However, we can probably make assumptions and generate a valid calculation based on the identified selection.

For example, consider a *Time Intelligence* calculation group with the following calculation items:

- Current: current period, no transformations applied
- PY: previous year
- YOY: year-over-year
- YTD: year-to-date
- PYTD: previous year-to-date
- YTDOYTD: year-to-date over year-to-date

Each item can be computed separately in a report where two or more visuals are included in the visual, like the columns of the following example.

![](https://cdn.sqlbi.com/wp-content/uploads/image9-44.png)

However, if the calculation item is not part of the visual and can be selected in a slicer, we have a report that displays one combination at a time. The following report shows the year-to-date of *Sales Amount* in 2023 and 2024.

![](https://cdn.sqlbi.com/wp-content/uploads/image10-38.png)

We can see the previous year-to-date as well – in the following example, the data displayed for 2024 are the values from 2023 in the screenshot above.

![](https://cdn.sqlbi.com/wp-content/uploads/image11-29.png)

However, let us say we want to achieve the same result, a theoretical PYTD, if the user selects PY and YTD.

![](https://cdn.sqlbi.com/wp-content/uploads/image12-21.png)

Similarly, if the user selects YOY and YTD, we want to produce YTDOYTD. We can implement this technique by comparing the values in the filter context with the list of items we want to intercept and by then applying the proper calculation item to the filter context to execute the correct calculation:

```dax


calculationGroup

    multipleOrEmptySelectionExpression = ```

        VAR sel_YTD_PY = { "YTD", "PY" }

        VAR sel_YTD_YOY = { "YTD", "YOY" }

        VAR selection = VALUES ( 'Time Intelligence'[Name] )

        RETURN SWITCH (

            TRUE,

            COUNTROWS ( INTERSECT ( selection, sel_YTD_PY ) ) == COUNTROWS ( sel_YTD_PY ) 

                && COUNTROWS ( selection ) == COUNTROWS ( sel_YTD_PY ),

                    CALCULATE ( SELECTEDMEASURE(), 'Time Intelligence'[Name] = "PYTD" ),

            COUNTROWS ( INTERSECT ( selection, sel_YTD_YOY ) ) == COUNTROWS ( sel_YTD_YOY )

                && COUNTROWS ( selection ) == COUNTROWS ( sel_YTD_YOY ),

                    CALCULATE ( SELECTEDMEASURE(), 'Time Intelligence'[Name] = "YTDOYTD" ),

            ERROR ( "Invalid Time Intelligence selection" )

        )

        ```

```

## Understanding the differences between empty selection and no selection

You may have noticed that the *multipleOrEmptySelectionExpression* property used to intercept the multiple selection has a name that includes “empty”. This name could be confusing because you may expect that it is invoked when no items are selected in the slicer. However, an empty selection is not the same as no selection.

In the filter context, and specifically in calculation groups, “no selection” means that no filters are active on the inspected column/table. Here, “no filters” means no direct or indirect filters, such as the propagation of cross filters from other columns or tables. Thus, if no items are selected for a column on a slicer in a report (and there are no filters on the same column in other visuals or the filter pane), we are in a “no filters” condition.

The “empty selection” statement means that even though a filter is active in the filter context, no values are visible for the column after applying the filters. This may sound like a surprising situation for a slicer used for a calculation group, but it can happen in the following scenario where you:

1. Publish a semantic model with a Time Intelligence calculation group.
2. Create a report connected to the published semantic model and use a slicer for the Time Intelligence calculation group.
3. Select “YTD” in the slicer and save the report.
4. Modify the published semantic model by renaming “YTD” to “Year-to-Date”.
5. Open the report saved before: the YTD filter produces an empty selection because no calculation items are named “YTD.”

This is why “Empty” is intercepted in the same event used for the multiple selection: it is a condition where there is an active filter on the calculation group, and you intercept the result of that filter that produces a number of calculation items other than one: it could be two or more, or it could be zero.

The “no selection” condition is different and is managed by using a DAX expression in a different property.

## Intercepting no selections in calculation groups

The intuitive way to think about “no selection” is to assign a sort of “default selection” for a slicer. However, using “no selection” as a “default selection could be misleading because there would be no evidence in the slicer that although no items are selected, a specific action is carried out instead. Nevertheless, for the specific case of calculation groups, you may want to intercept the calculation anyway. For example, we consider the currency conversion.

The demo file has transactions recorded in USD, but the original currency of the transaction might be different. Therefore, if we slice the amount by *Currency*, we see the values in USD – the internal currency we have used in all previous examples.

![](https://cdn.sqlbi.com/wp-content/uploads/image13-19.png)

When the visual is sliced by currency, we need to display the amount in the original currency. Because the original currency amount is unavailable, we can calculate it using a currency exchange rate table. We compute the calculation at query time to avoid storing additional data in the model. However, we have several measures that display those amounts, and we want to apply the currency transformation to all of them.

A traditional approach could be to use a calculation group with a calculation item, which when activated, executes the currency conversion. However, we should always select that calculation item in every report. Ideally, that calculation item should be automatically applied whenever any column of the *Currency* table is used in a report. With the “no selection” choice, we can create a calculation group that has only one calculation item that returns the original value, and a “no selection” expression that executes the conversion when the calculation group is not used or referenced in a report. In other words, the currency conversion is active unless explicitly disabled!

The **noSelectionExpression** property in the calculation group defines the DAX expression that intercepts all the measure references when there are no active calculation items for the calculation group. The user could “disable” the *noSelectionExpression* of a hidden calculation group by applying a filter on a calculation item. For example, you could provide a “No conversion” calculation item that returns [[SELECTEDMEASURE]], like in the following example. Or you could duplicate the *noSelectionExpression* code to ensure that all the measures are intercepted, no matter what the user does.

```dax


calculationGroup

    precedence: 60



    noSelectionExpression = ```

            

        IF (

            ISCROSSFILTERED ( 'Currency' ),

            CALCULATE (

                VAR SelectedCurrency = SELECTEDVALUE ( 'Currency'[Currency Code], "USD" )

                VAR MeasureName = SELECTEDMEASURENAME() 

                VAR SkipConversion = CONTAINSSTRING ( MeasureName, "#" ) 

                                       || CONTAINSSTRING ( MeasureName, "%" )

                RETURN 

                    IF (

                        SkipConversion || SelectedCurrency = "USD",

                        SELECTEDMEASURE (),

                        VAR DailyAmount = 

                            CALCULATETABLE(

                                SUMMARIZECOLUMNS(

                                    'Date'[Date],

                                    Currency[Currency Code],

                                    "@Amount", SELECTEDMEASURE (),

                                    "@ExchangeRate", VALUES(ExchangeRate[Exchange])

                                ),

                                ExchangeRate[FromCurrency] = "USD",

                                USERELATIONSHIP ( 

                                    ExchangeRate[ToCurrency], 

                                    'Currency'[Currency Code] 

                                )

                            )

                        VAR Result =

                            SUMX ( DailyAmount, [@Amount] * [@ExchangeRate] )

                        RETURN

                            Result

                    )

            ),

            SELECTEDMEASURE ()

        )

        

            ```



        formatStringDefinition =

            IF (

                ISCROSSFILTERED ( 'Currency' ),

                VAR MeasureName = SELECTEDMEASURENAME() 

                VAR SkipConversion = CONTAINSSTRING ( MeasureName, "#" ) 

                                         || CONTAINSSTRING ( MeasureName, "%" )

                RETURN IF (

                    SkipConversion,

                    SELECTEDMEASUREFORMATSTRING(),

                    SELECTEDVALUE ( 

                        'Currency'[Currency Format], 

                        SELECTEDMEASUREFORMATSTRING () 

                    )

                ),

                SELECTEDMEASUREFORMATSTRING ()

            )



    calculationItem 'No conversion' = SELECTEDMEASURE()

```

The result shows the amount converted with a proper format string, which clarifies that the amount is represented in a different currency.

![](https://cdn.sqlbi.com/wp-content/uploads/image14-13.png)

Pay attention to the code that implements the calculation item value and the format string: the initial [[ISCROSSFILTERED]] is redundant in this simple example. However, it is a helpful technique to optimize the execution when the calculation is unnecessary because the *Currency* table is not referenced in the report.

In this last report, the total shows the amount in USD without including a currency symbol, because otherwise that symbol would appear in all other reports. You may want to hide the total row from the report to avoid showing this information, or change the format string of the original measure so that US$ appears in the total row and in all other reports that does not filter or group by currency.

## Conclusions

The *multipleOrEmptySelectionExpression* and *noSelectionExpression* properties of the calculation group give you control of the calculation whenever multiple calculation items are selected or if there is no active selection. These properties can simplify the use of calculation groups and avoid misunderstandings about the result of a report with calculation groups: the developer of the semantic model can control any selection made by the user and define a specific behavior, including generating an error for unsupported combinations.

While extremely powerful, these properties hide an increase in execution complexity that can negatively affect the performance of all the reports. Calculation groups already apply a tax to the query plan; using these additional properties adds a burden to the DAX engine. Therefore, always test the performance before releasing model updates that use these new features. The [Optimizing DAX](https://www.sqlbi.com/p/optimizing-dax-video-course/) video course offers a complete overview of the performance aspect.

[[ERROR]]

Raises a user specified error.

`ERROR ( <ErrorText> )`

[[MAX]]

Returns the largest value in a column, or the larger value between two scalar expressions. Ignores logical values. Strings are compared according to alphabetical order.

`MAX ( <ColumnNameOrScalar1> [, <Scalar2>] )`

[[SELECTEDMEASURE]]

Context transition

Returns the measure that is currently being evaluated.

`SELECTEDMEASURE ( )`

[[ISCROSSFILTERED]]

Returns true when the specified table or column is crossfiltered.

`ISCROSSFILTERED ( <TableNameOrColumnName> )`
