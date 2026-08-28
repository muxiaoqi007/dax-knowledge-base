---
title: "Clean data = faster reports"
url: "https://dax.tips/2019/11/28/clean-data-faster-reports"
published: "2019-11-27"
updated: "2019-11-27"
source: "dax.tips"
---

Did you know, Analysis Services (the engine behind Power BI) keeps track of the number of referential integrity (RI) issues in your model? It uses this information to decide if some calculations can take advantage of specific optimisations.

When RI issues exist in an IMPORT model, complex expressions will not be able to use all optimisations available to them.

When data is clean, and no RI issues exist in the model, the same expressions can potentially run much faster without any other change.

This rule is only true of models that run in import mode. I highly recommend Direct Query models use tips outlined in [[tip-to-speed-up-direct-query|this article]].

### What is a RI Violation?

Something considered a “referential integrity violation” is where key-values in a FACT table are missing from the DIMENSION table. The FACT table has a strong 1-to-many relationship to the Dimension table.

In the example below, the last record in the FACT sales table has a Product ID = 4. This value does not appear in the Product ID column in the Dim Product table. This additional value is enough for AS to mark the Dim Product table as having a RI violation.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/11/B5-2.png?resize=465%2C409&ssl=1)

## How can I quickly check

Using DAX Studio, connect to your fully populated Power BI Desktop model (this also works for Analysis Services).

Once you’ve connected to your data model, click the DMV table at the bottom of the field list to display all the DMV queries you can run.

Double click the DISCOVER\_STORAGE\_TABLES DMV which will throw a basic SELECT \* version of the DMV into the query window.

Or, you can paste the following version to the query window to run against your model.

```
select 
	[Database_name] , 
	[Dimension_Name] , 
	[RIVIOLATION_COUNT] 
from $SYSTEM.DISCOVER_STORAGE_TABLES
WHERE
	[RIVIOLATION_COUNT] > 0
```

When this query gets run, it will show the name of any table with a RI violation. The value in the last column represents the number of child tables with RI integrity issues. This value is not the number of missing keys.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/11/B3-1.png?fit=1000%2C695&ssl=1)

### Data Loading

Once AS has loaded (or refreshed) tables that use the Import storage mode, a quick check/scan is performed over the unique values contained either side of every strong relationship. When values exist in many column that don’t exist in the column on the one-side, this is status is recorded internally.

Then, when queries get fired, the internal table is checked to see if RI violations exist as part of building DAX query plans.

### How can I fix it?

You can either remove the offending record from the FACT table or add the value to your DIM table. The most straightforward approach is probably to add the missing values to your DIM table.

You can use a variation on the following DAX calculation to give you a list of the missing keys.

```dax
EVALUATE
	CALCULATETABLE(
		VALUES('Fact Sales'[Product ID]) ,
		ISBLANK('Dim Product'[Product ID])
		)

```

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/11/B4-2.png?resize=648%2C325&ssl=1)

### Why do I care?

If you have complex DAX expressions in your model, such as SWITCH statements, or visuals with multiple measures – you want to be sure these are taking full advantage of all internal optimisations available.

The underlying AS engine is continually evolving, so this is a bit of a moving target. However, if you take one thing away from this article, it is this – calculations in models that have 0 RI violations can potentially run significantly faster than in a similar model that has RI violations.

I have seen real-life examples where merely adding a few rows of data to a DIM table resulted in queries running in half the time.

This rule is one of those Black Box scenarios, where you aren’t sure of the benefit – but if you have even just a slither of OCD, it’s probably an excellent check to add to models you care.

5
20
votes

Article Rating
