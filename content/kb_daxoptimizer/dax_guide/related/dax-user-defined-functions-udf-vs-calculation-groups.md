---
title: "DAX user-defined functions (UDF) vs. calculation groups"
url: "https://www.sqlbi.com/articles/dax-user-defined-functions-udf-vs-calculation-groups"
published: "2026-03-25"
updated: 
source: "sqlbi.com"
---

# DAX user-defined functions (UDF) vs. calculation groups

> 发布：2026-03-25

The introduction of user-defined functions (UDFs) in DAX changes the way we think about code reuse. Before UDFs existed, calculation groups were the only mechanism for sharing common logic across multiple calculations. Many developers adopted calculation groups not because they were the ideal tool for code reuse, but because there was no alternative.

Now that user-defined functions are available, it is time to revisit this practice. User-defined functions and calculation groups serve fundamentally different purposes. Understanding the distinction between the two is essential for building well-organized, efficient semantic models.

## Three tools, three purposes

A semantic model offers three different tools to a DAX developer, and each serves a distinct role:

- **Measures** expose specific calculations to the end user in the semantic model.
- **Calculation groups** expose common filters or transformations that can be applied to any measure.
- **User-defined functions** allow a developer to write code once and reuse it everywhere, in measures, calculated columns, security roles, and also calculation groups!

The key distinction is the target audience. Measures and calculation groups are visible to the report user; they appear in reports and visuals. On the other hand, user-defined functions are invisible to report users. A function is simply a tool for the developer to arrange code in the best possible way. The report user never directly sees, selects, or interacts with a function.

This separation leads to a clear design principle: the decision to expose a feature as a measure or as a calculation group is a user-facing decision. By contrast, the sharing of business logic is an internal implementation detail of the semantic model.

## When to use calculation groups

Calculation groups remain the best tool when the goal is to provide the end user with a choice that applies to all measures in a visual. Two scenarios illustrate this well.

The first scenario is a common filter. When users need to select a filter that applies to every measure in a report, a calculation group lets them do so through a single slicer. For example, a calculation group with items like “Current Month” and “Last Quarter” allows the user to choose which period to analyze. The selected calculation item is then applied to all the measures in the visual without requiring the developer to create separate measure variants for each combination.

![](https://cdn.sqlbi.com/wp-content/uploads/image1-128.png)

The second scenario is a common transformation. When all the numbers in a visual should be divided by 1,000 or by 1,000,000 for readability, a calculation group applies that transformation to every measure at once. The user simply selects the desired scale factor, and the transformation is applied to all the measures.

![](https://cdn.sqlbi.com/wp-content/uploads/image2-124.png)

In both cases, the defining characteristic is the same: the calculation group applies a single transformation to all measures without distinction. This is precisely what makes calculation groups valuable to the report user; the same characteristic limits their flexibility when the goal is something different.

## A practical example: new and returning customers

Consider a scenario in which the user must choose between analyzing new and returning customers. A “new customer” is one whose first purchase falls within the current filter context; a “returning customer” is one whose first purchase occurred before the current filter context. This is a good candidate for a calculation group because the same segmentation logic applies to every measure in the visual. Whether the user is viewing *Sales Amount*, *# Orders*, *Margin*, or *Margin %*, the filter restricts the data to the same subset of customers.

![](https://cdn.sqlbi.com/wp-content/uploads/image3-111.png)

The user selects “New customers” or “Returning customers” from a slicer, and every measure in the visual is filtered accordingly. This is a user-facing decision: the user chooses which customer segment to analyze. A calculation group is the correct tool for this purpose:

Calculation item in Customers Group table

```dax


New customers = 

VAR CustomersWithNewDate =

    CALCULATETABLE (

        SUMMARIZECOLUMNS (

            Sales[CustomerKey],

            "@NewCustomerDate", 

                CALCULATE ( MIN ( Sales[Order Date] ) )

        ),

        ALLEXCEPT ( Sales, Customer )

    )

VAR NewCustomers =

    FILTER (                              

        CustomersWithNewDate,

        [@NewCustomerDate] IN VALUES ( 'Date'[Date] )

    )

VAR Result =

    CALCULATE(

        SELECTEDMEASURE(),

        NewCustomers

    )

RETURN Result

```

Calculation item in Customers Group table

```dax


Returning customers = 

VAR MinDate = MIN ( 'Date'[Date] )

VAR CustomersWithNewDate =

    CALCULATETABLE (

        SUMMARIZECOLUMNS (

            Sales[CustomerKey],

            "@NewCustomerDate", 

                CALCULATE ( MIN ( Sales[Order Date] ) )

        ),

        ALLEXCEPT ( Sales, Customer )

    )

VAR ExistingCustomers =

    FILTER (

        CustomersWithNewDate,

        [@NewCustomerDate] < MinDate

    )

VAR ReturningCustomers =

    SELECTCOLUMNS (

        ExistingCustomers,

        "CustomerKey", Sales[CustomerKey]

    )

VAR Result =

    CALCULATE(

        SELECTEDMEASURE(),

        KEEPFILTERS ( ReturningCustomers )

    )

RETURN Result

```

This example works well because the calculation item applies the same segmentation logic to every measure without distinction. The user makes a single choice, and the entire visual responds. Please note that a full description of the new and returning customers pattern is available in the [New and returning customers](https://www.daxpatterns.com/new-and-returning-customers/) article at [www.daxpatterns.com](http://www.daxpatterns.com).

## Why calculation groups are not ideal for code reuse

Despite their versatility, calculation groups are not a good tool for sharing code across multiple calculations. The reason is structural: calculation groups, like measures, do not have parameters.

In DAX, the typical way to pass information to shared code in a measure or calculation group is through the filter context. A developer modifies the filter context before calling a measure or applying a calculation item, and the shared code reads the information from that modified context. This approach works, but it is expensive. Transferring information through the filter context may require additional work at query time, thereby degrading performance.

A user-defined function accepts parameters directly. The function is expanded in the query plan, much like macros in other languages like C/C++. Because the parameters are resolved before the code is executed, the result is a more efficient query plan that does not generate additional work at execution time.

The performance difference may be negligible on a small model with a simple calculation. However, as the complexity of the shared logic grows, and especially when the same logic is invoked multiple times within a single query, the overhead of passing parameters through the filter context adds up. Using a function with explicit parameters can help us avoid this overhead.

## User-defined functions for business logic

User-defined functions are the primary tool for sharing and reusing code within and across models (more details here: [Model-dependent and model-independent user-defined functions in DAX](https://www.sqlbi.com/articles/model-dependent-and-model-independent-user-defined-functions-in-dax/)). Whenever a piece of business logic is complex enough to warrant a single definition, especially logic containing parameters that might vary, or rules that should only be defined in one place, a UDF is the appropriate choice.

We have two examples to clarify this.

The first scenario is encapsulating business logic. Consider the customer segmentation from the previous section. The logic that identifies new customers is business logic: it defines what “new” means. If we place that logic in a function, we can reuse it both in the calculation item and in a standalone measure. The business rule is defined once; every consumer of that rule, whether a calculation item or a measure, calls the same function. The business logic is defined in three model-independent functions embedded in two model-dependent functions that are referenced later in calculation items and measures:

Function

```dax


DaxPatterns.NewReturning.Absolutes.CustomersWithNewDate = (

    TxCustomerKeyColumn: COLUMNREF, 

    CustomerTable: TABLEREF, 

    TxDateColumn: COLUMNREF 

) =>

    CALCULATETABLE (

        SUMMARIZECOLUMNS (

            TxCustomerKeyColumn,

            "@NewCustomerDate", 

                CALCULATE ( MIN ( TxDateColumn ) )

        ),

        ALLEXCEPT ( TABLEOF ( TxCustomerKeyColumn ), CustomerTable )

    )

```

Function

```dax


DaxPatterns.NewReturning.Absolutes.NewCustomers = ( 

    TxCustomerKeyColumn: COLUMNREF, 

    CustomerTable: TABLEREF, 

    TxDateColumn: COLUMNREF, 

    DateColumn: COLUMNREF 

) => 

    FILTER (                              

        DaxPatterns.NewReturning.Absolutes.CustomersWithNewDate ( 

            TxCustomerKeyColumn, 

            CustomerTable, 

            TxDateColumn 

        ),

        [@NewCustomerDate] IN VALUES ( DateColumn )

    )

```

Function

```dax


DaxPatterns.NewReturning.Absolutes.ReturningCustomers = (

    TxCustomerKeyColumn: COLUMNREF, 

    CustomerTable: TABLEREF, 

    TxDateColumn: COLUMNREF, 

    DateColumn: COLUMNREF

) =>

    VAR MinDate = MIN ( DateColumn )

    VAR ExistingCustomers =

        FILTER (

            DaxPatterns.NewReturning.Absolutes.CustomersWithNewDate ( 

                TxCustomerKeyColumn, 

                CustomerTable, 

                TxDateColumn 

            ),

            [@NewCustomerDate] < MinDate

        )

    VAR ReturningCustomers =

        SELECTCOLUMNS (

            ExistingCustomers,

            "CustomerKey", TxCustomerKeyColumn

        )

    RETURN ReturningCustomers

```

Function

```dax


Local.NewCustomers = () => 

DaxPatterns.NewReturning.Absolutes.NewCustomers ( 

    Sales[CustomerKey], 

    Customer, 

    Sales[Order Date], 

    'Date'[Date] 

)

```

Function

```dax


Local.ReturningCustomers = () => 

DaxPatterns.NewReturning.Absolutes.ReturningCustomers ( 

    Sales[CustomerKey], 

    Customer, 

    Sales[Order Date], 

    'Date'[Date] 

)

```

The calculation items can be defined using much shorter expressions that reference the model-dependent functions:

Calculation item in Customers Fx table

```dax


New customers = 

CALCULATE (

    SELECTEDMEASURE (),

    NewCustomers ()

)

```

Calculation item in Customers Fx table

```dax


Returning customers = 

CALCULATE (

    SELECTEDMEASURE (),

    ReturningCustomers ()

)

```

The same user-defined functions can be used in measures:

Measure in Sales table

```dax


Sales New Customers = 

CALCULATE ( 

    [Sales Amount],

    Local.NewCustomers ()

)

```

Measure in Sales table

```dax


Sales Returning Customers = 

CALCULATE ( 

    [Sales Amount],

    Local.ReturningCustomers ()

)

```

The business logic lives in exactly one place. If the definition of “new customer” changes, we update the function, and every consumer reflects the change.

The measures we created help us illustrate the second scenario: side-by-side visuals. Suppose we want a report that shows both the standard *Sales Amount* and the *Sales Amount* for new customers in the same visual, as two separate columns, together with two other columns with *Margin* and *Margin %*. If we rely solely on a calculation group, the selected calculation item is applied to every measure in the visual. We cannot have one measure with the calculation item active (*Sales Amount*) and the other two measures without the “new customer” (*Margin* and *Margin %*) in the same visual. By defining a standalone measure that calls the function, we can place both the *Sales Amount* and the *Sales New Customers* measures in the same visual without interference.

![](https://cdn.sqlbi.com/wp-content/uploads/image4-105-scaled.png)

If you are interested in another practical, step-by-step example of this approach that describes how we transform an existing calculation into a more generic function, read the article [Creating functions for the like-for-like DAX pattern](https://www.sqlbi.com/articles/creating-functions-for-the-like-for-like-dax-pattern/).

## When not to use a function

One could argue that every single calculation should be written as a UDF, with measures and calculation items serving merely as function calls with the appropriate parameters. We do not suggest going to this extreme. If a measure contains a simple [[SUM]] or a straightforward subtraction, there is no benefit in wrapping it inside a function. Moving a simple operation into a function only hides the calculation and makes the code harder to read.

The guideline is pragmatic: use a function when the logic is complex enough to benefit from a single definition, when it contains parameters that might vary, or when the same business rule must be applied in multiple places. For simple calculations, write the code directly in the measure.

For example, the calculation items in the Period calculation group may be simple enough not to require a separate function definition. Consider the fact that the actual implementation in the sample file is longer because of the need to simulate a specific “current” date:

Calculation item in Period table

```dax


Current Year = 

CALCULATE ( 

    SELECTEDMEASURE(),

    PARALLELPERIOD ( 'Date'[Date], 0, YEAR )

)

```

Calculation item in Period table

```dax


Last Year = 

CALCULATE ( 

    SELECTEDMEASURE(),

    PARALLELPERIOD ( 'Date'[Date], -1, YEAR )

)

```

## Conclusions

User-defined functions and calculation groups are complementary tools that serve different audiences. A calculation group is a feature exposed to report users; it lets them apply a common filter or transformation to all measures in a visual. A user-defined function is invisible to the user; it is a tool for the developer to organize business logic in one place and reuse it wherever needed.

The design of a semantic model benefits from keeping this distinction clear. Decide what the user needs to see and interact with; that determines whether to use a measure or a calculation group. Then decide how to implement the underlying logic efficiently, and consider user-defined functions as a tool available for that implementation.

When business logic is complex or shared across multiple measures, place it in a function. Let measures and calculation items call that function with the appropriate parameters. The result is a semantic model that is easier to maintain, consistent in its business rules, and more efficient in its query plans.

[[SUM]]

Adds all the numbers in a column.

`SUM ( <ColumnName> )`
