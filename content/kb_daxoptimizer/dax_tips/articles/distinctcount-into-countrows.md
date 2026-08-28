---
title: "Optimize Your Power BI Model: Use COUNTROWS for Distinct Counts"
url: "https://dax.tips/2026/08/07/distinctcount-into-countrows"
published: "2026-08-06"
updated: "2026-08-07"
source: "dax.tips"
---

![](https://i0.wp.com/dax.tips/wp-content/uploads/2026/08/Designer-5.jpg?resize=1000%2C667&ssl=1)

Counting transactions is one of the most common things a retail model is asked to do. How many transactions did we do in March. How many in New Zealand. How many involved a T-shirt.

The obvious measure is the one everybody writes:

```
Transactions = DISTINCTCOUNT ( FactTransaction[TransactionID] )
```

It is correct, and on a large fact table it is one of the most expensive things you can ask the storage engine to do.

This post is about a modelling technique that answers a large share of those questions with a plain `COUNTROWS` instead, and meaningfully reduces the work for the questions that still need a real distinct count. It does not eliminate `DISTINCTCOUNT`. It aims to stop you paying for it when you do not need it, and to hand it a much smaller pile of rows when you do.

The examples below are retail, because a till receipt is the easiest way to picture the idea. The principle is not retail specific. It applies anywhere you count distinct instances of something that belongs to a natural parent: claims in insurance, work orders in manufacturing, sessions in telemetry, encounters in healthcare, consignments in logistics, calls in a contact centre. Substitute your own nouns as you go.

## A thirty second check before you go further

Before you build anything, get two numbers. The row count of your fact table, and the cardinality of the column you want to distinct count.

```
SELECT



COUNT(*)                       AS FactRows,



COUNT(DISTINCT TransactionID)  AS DistinctTransactions



FROM dbo.FactTransaction;
```

Or straight from the model:

```dax
EVALUATE



ROW (



"FactRows",             COUNTROWS ( FactTransaction ),



"DistinctTransactions", DISTINCTCOUNT ( FactTransaction[TransactionID] )



)

```

The ratio between them tells you how much of this technique is available to you, and it is worth more than a rough feel, because you can put a hard bound on it.

Call the rows `R` and the distinct values `D`. The excess is `E = R - D`. Every transaction with more than one line contributes at least one row to that excess, so **at most `E` of your transactions can be multi line**. A two line transaction is the worst case for rows per unit of excess, so **at most `2E` rows can belong to multi line transactions**. Everything else is a single line transaction, and single line transactions are exactly the ones a row count handles.

Here is a real model I looked at recently. 202 million rows, and 193 million distinct transaction IDs.

- Excess `E` is 9 million.
- So at most 9 million of the 193 million transactions have more than one line. At least

**95 percent are single line**.

- At most 18 million rows belong to multi line transactions. That is under **9 percent** of the table.

In other words, more than 90 percent of that fact table can be counted with `COUNTROWS`, and `DISTINCTCOUNT` only ever has to look at the sliver that is left. Two numbers, no modelling work, and you already know the technique is worth doing.

| Distinct / Rows | Average lines | What it suggests |
| --- | --- | --- |
| Above 90% | Under 1.1 | Nearly every transaction is a single line. Large win, and the bound above proves it. |
| 40% to 90% | 1.1 to 2.5 | Worth doing. The ratio is now a hint rather than a proof, so size it with the distribution query further down. |
| Under 20% | Over 5 | The single line split is probably not worth the complexity. The grain table in step one may still be. |

The forty percent row deserves a caveat, because I do not want to oversell the arithmetic. At that ratio the bound stops being useful, and in principle you could have no single line transactions at all, every basket holding exactly two or three items. In practice real transaction data is not shaped like that. Basket size distributions have a heavy spike at one, so a moderate ratio still usually hides a large single line population. That is an observation about real data, not a proof, so confirm it with the distribution query rather than assuming it.

One thing this check does not measure is the other half of the technique. The grain table in step one pays off based on how many of your queries filter only by safe dimensions like date and store, and that has nothing to do with this ratio. Even a model full of large baskets can benefit from it.

## Why DISTINCTCOUNT costs so much

It is worth being precise about why this one aggregation behaves so differently from the others.

When the storage engine runs a `SUM` or a `COUNTROWS`, it splits the table into segments, hands each segment to a thread, and each thread produces a single number. Merging those numbers is arithmetic. Memory per thread is constant. It scales beautifully across cores, which is exactly what you want from a columnar engine.

`DISTINCTCOUNT` cannot work that way. The same transaction ID can appear in more than one segment, so a thread cannot report a number. It has to report the *set* of values it saw. The engine then has to union those sets before it can count them. That union is the expensive part, and its cost scales with the cardinality of the column, not just the number of rows.

Three consequences follow from that:

- **Memory scales with cardinality.** Ten million distinct transaction IDs means a very large set to build and merge, per query.

- **Grouping multiplies it.** A distinct count by product needs a separate set per product. The engine cannot reuse work between groups.

- **The column has to be read.** A high cardinality ID column is usually one of the worst compressed columns in your model. `COUNTROWS` never has to touch it.

Everything below is an attempt to avoid all three.

## The observation that makes this work

Look at what a retail fact table actually looks like when a customer buys more than one item.

```
FactTransaction



+--------+----------+----------+-------------+-----+



| TranID | DateKey  | StoreKey | ProductKey  | Qty |



+--------+----------+----------+-------------+-----+



| 1001   | 20260301 | NZ-012   | T-Shirt     |   1 |   <- 1 line



| 1002   | 20260301 | NZ-012   | Jeans       |   1 |   \



| 1002   | 20260301 | NZ-012   | Socks       |   2 |    >  1 transaction, 3 lines



| 1002   | 20260301 | NZ-012   | T-Shirt     |   1 |   /



| 1003   | 20260302 | NZ-044   | Cap         |   1 |   <- 1 line



+--------+----------+----------+-------------+-----+



^^^^^^^^^^^^^^^^^^^^  ^^^^^^^^^^^^



repeat within      varies within



a transaction      a transaction
```

Transaction 1002 has three lines. `DateKey` is the same on all three. `StoreKey` is the same on all three. `ProductKey` is different on every one.

That is not a coincidence, it is the physical reality of a till. One customer, one store, one moment in time, several items. In database terms, `TransactionID` **functionally determines** `DateKey` and `StoreKey`. Give me a transaction ID and I can tell you exactly one date and exactly one store. Give me a transaction ID and I cannot tell you one product.

That asymmetry is the whole technique. Some of your dimensions are safe. Some are not. Once you know which is which, you can treat them differently.

## Step one: build a transaction grain table

If a transaction has exactly one date and exactly one store, you can build a table with one row per transaction carrying those keys.

```
SELECT



MIN(DateKey)  AS DateKey,



MIN(StoreKey) AS StoreKey



FROM dbo.FactTransaction



GROUP BY TransactionID;
```

Two things about that query are worth dwelling on.

**`MIN` is safe here, and only here.** Normally taking `MIN` of a dimension key inside a group by is a bug waiting to happen. It is safe in this case precisely because we established the functional dependency first. There is only one value to choose from, so `MIN` picks it. If the dependency does not hold, this query silently produces wrong data. Validate before you trust it, and there is a query for that further down.

**`TransactionID` is not in the SELECT list.** This is the bit people find odd, and it is the best part. You do not need it. Every row in this table *is* a transaction, so the row count is the transaction count. Leaving the column out means the highest cardinality column in your model never gets built at all. On a large model that alone is a serious memory saving.

```
Transaction Grain (one row per transaction, no ID column)



+----------+----------+



| DateKey  | StoreKey |



+----------+----------+



| 20260301 | NZ-012   |   <- was 1001



| 20260301 | NZ-012   |   <- was 1002



| 20260302 | NZ-044   |   <- was 1003



+----------+----------+



COUNTROWS ( 'Transaction Grain' ) = 3
```

Note that the first two rows are identical. That is fine. VertiPaq does not deduplicate rows, so the count stays correct. If it did deduplicate, this whole approach would collapse.

Wire the same dimensions up to it. `Date[DateKey]` to `Transaction Grain[DateKey]`, `Store[StoreKey]` to `Transaction Grain[StoreKey]`, single direction, dimension to grain, exactly like the fact table. Hide the table from users.

Now this:

```
Transactions = COUNTROWS ( 'Transaction Grain' )
```

gives the right answer for **transactions in March 2026**, for **transactions in New Zealand**, and for **transactions in New Zealand in March 2026**. Geography works because the store hierarchy hangs off `Store`, and store is safe, so everything above store is safe too. Same for anything you hang off `Date`.

It is a pure row count over a narrow table. It parallelises across every core, uses almost no memory, and never reads a high cardinality column.

## Step two: the problem with product

Now ask a different question. How many transactions included a T-shirt.

The grain table cannot answer it. It has no product column, and it cannot have one, because a transaction does not have *a* product, it has several. If you filter Product and then run `COUNTROWS` over the grain table, the product filter simply does not reach it and you get every transaction in the date and store context. Badly wrong, and wrong in the direction that looks plausible, which is worse.

```
Filter: Product = T-Shirt



Transaction Grain          FactTransaction



+----------+--------+      TranID 1001 -> T-Shirt        yes



| 20260301 | NZ-012 |      TranID 1002 -> Jeans, Socks,  yes



| 20260301 | NZ-012 |                     T-Shirt



| 20260302 | NZ-044 |      TranID 1003 -> Cap            no



+----------+--------+



COUNTROWS(Grain) = 3       correct answer = 2
```

So for product, and for any other dimension that varies within a transaction, you are back to needing a genuine distinct count.

## Step three: split the fact table by line count

Here is where you claw most of it back.

Some transactions have exactly one line. In retail that is a big share of them: someone buys a coffee, a newspaper, one T-shirt and leaves. For those transactions, one row in the fact table means one transaction. There is nothing to deduplicate.

So precompute a flag during ETL:

```
SELECT



f.*,



CASE WHEN t.LinesInTransaction = 1 THEN 1 ELSE 0 END AS IsSingleLineTransaction



FROM dbo.FactTransaction f



JOIN (



SELECT TransactionID, COUNT(*) AS LinesInTransaction



FROM dbo.FactTransaction



GROUP BY TransactionID



) t ON t.TransactionID = f.TransactionID;
```

Define the flag on **line count**, not on distinct product count. The two are usually almost the same, but they are not identical. If a transaction has two lines for the same product, a scan error or a split quantity, then it has one distinct product but two rows. Counting rows would count it twice. Line count is the definition that makes `COUNTROWS` exactly right, so use that one.

That splits the fact table into two partitions that do not overlap:

```
                      FactTransaction
                            |
          +-----------------+------------------+
          |                                    |
   IsSingleLine = 1                     IsSingleLine = 0
   exactly one row per transaction      many rows per transaction
          |                                    |
     COUNTROWS                          DISTINCTCOUNT
     cheap, parallel, no                still expensive, but over
     high cardinality read              a much smaller set of rows
          |                                    |
          +-----------------+------------------+
                            |
                           SUM
```

Because every transaction is in exactly one of those two buckets, you can just add the results.

```dax
Transactions =



VAR SingleLine =



CALCULATE (



COUNTROWS ( FactTransaction ),



FactTransaction[IsSingleLineTransaction] = TRUE ()



)



VAR MultiLine =



CALCULATE (



DISTINCTCOUNT ( FactTransaction[TransactionID] ),



FactTransaction[IsSingleLineTransaction] = FALSE ()



)



RETURN



SingleLine + MultiLine

```

This version is **always correct**, whatever is filtering. It does not care about product, or promotion, or anything else, because it works entirely on the fact table. It has not removed `DISTINCTCOUNT`, it has just stopped feeding it the rows that never needed it.

If sixty percent of your transactions are single line, you have moved sixty percent of the distinct counting into a row count. That is not a rewrite of the query plan, it is a smaller input, and for this aggregation a smaller input is most of the battle.

## Step four: route between the two

Now put the pieces together. The grain table is much faster but only valid sometimes. The split measure is always valid but slower. So test which situation you are in.

`ISFILTERED` and `ISCROSSFILTERED` are formula engine operations that inspect the filter context. They do not scan anything and they cost effectively nothing.

```dax
Transactions =



VAR GrainIsSafe =



NOT ISCROSSFILTERED ( 'Product' )



&& NOT ISFILTERED ( FactTransaction[ProductKey] )



&& NOT ISFILTERED ( FactTransaction[IsSingleLineTransaction] )



VAR FastPath =



COUNTROWS ( 'Transaction Grain' )



VAR SingleLine =



CALCULATE (



COUNTROWS ( FactTransaction ),



FactTransaction[IsSingleLineTransaction] = TRUE ()



)



VAR MultiLine =



CALCULATE (



DISTINCTCOUNT ( FactTransaction[TransactionID] ),



FactTransaction[IsSingleLineTransaction] = FALSE ()



)



RETURN



IF ( GrainIsSafe, FastPath, SingleLine + MultiLine )

```

```
              Is anything filtering product?
                          |
            +-------------+-------------+
            |                           |
           NO                          YES
            |                           |
   COUNTROWS( Grain )          COUNTROWS( single line )
   one narrow scan             + DISTINCTCOUNT( multi line )
   fastest                     correct, and smaller than
                               the original
```

A note on what this routing actually saves, because it is easy to overclaim, and I did in the first version of this post.

DAX evaluates a VAR only when something uses it, so the branch you do not take does not have its value computed. In most cases that means the expensive storage engine work is avoided, and on a date and store query you are not paying for a distinct count scan. That is the saving this post is about and it is the part that matters.

The formula engine is less clear cut. Whether the untaken branch costs you anything there depends on the query plan, and specifically on whether the engine can resolve the condition before it starts executing. If `ISCROSSFILTERED ( 'Product' )` is uniform across every cell the query computes, the branch can be eliminated up front and the routing is close to free. If it is not uniform, the engine has to be able to produce both answers, and formula engine cost for both branches tends to show up.

The everyday case where it is not uniform is a matrix with subtotals. A `SUMMARIZECOLUMNS` with subtotals computes detail cells where product is filtered and total rows where it is not, all in one query. The condition is true for some cells and false for others, so there is nothing to prune.

So treat the storage engine saving as the real prize, and treat the routing test as cheap rather than free. Thanks to Marco Russo for the correction.

## Working it out on Adventure Works

You do not need retail data to try this. Adventure Works has the same shape. `FactInternetSales` has `SalesOrderNumber` playing the role of the transaction ID, with `SalesOrderLineNumber` for the lines. `OrderDateKey`, `CustomerKey` and `SalesTerritoryKey` are fixed within an order. `ProductKey` is not.

**First, prove the functional dependency.** Never assume it, and never take someone’s word for it. This query should return zero.

```
SELECT COUNT(*) AS OffendingOrders



FROM (



SELECT SalesOrderNumber



FROM dbo.FactInternetSales



GROUP BY SalesOrderNumber



HAVING COUNT(DISTINCT OrderDateKey)      > 1



OR COUNT(DISTINCT CustomerKey)       > 1



OR COUNT(DISTINCT SalesTerritoryKey) > 1



) x;
```

Anything above zero and that column does not belong in your grain table. Run it for every key you are considering, and run it again whenever the source changes.

**Second, find out if the technique is worth it.** This tells you how the orders split, which is the number that decides everything.

```
SELECT



LinesInOrder,



COUNT(*) AS Orders,



CAST(100.0 * COUNT(*) / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)) AS PctOfOrders



FROM (



SELECT SalesOrderNumber, COUNT(*) AS LinesInOrder



FROM dbo.FactInternetSales



GROUP BY SalesOrderNumber



) x



GROUP BY LinesInOrder



ORDER BY LinesInOrder;
```

If you only have the model and not the database, the same summary in DAX:

```dax
EVALUATE



VAR LinesPerOrder =



ADDCOLUMNS (



VALUES ( FactInternetSales[SalesOrderNumber] ),



"@Lines", CALCULATE ( COUNTROWS ( FactInternetSales ) )



)



RETURN



ROW (



"Orders",           COUNTROWS ( LinesPerOrder ),



"SingleLineOrders", COUNTROWS ( FILTER ( LinesPerOrder, [@Lines] = 1 ) ),



"MultiLineOrders",  COUNTROWS ( FILTER ( LinesPerOrder, [@Lines] > 1 ) ),



"RowsInMultiLine",  SUMX ( FILTER ( LinesPerOrder, [@Lines] > 1 ), [@Lines] )



)

```

`RowsInMultiLine` is the one to watch. That is the number of rows `DISTINCTCOUNT` still has to process after the optimisation. Compare it to the total row count and you have your expected saving before you build anything.

**Third, build the grain table.**

```
SELECT



MIN(OrderDateKey)      AS OrderDateKey,



MIN(CustomerKey)       AS CustomerKey,



MIN(SalesTerritoryKey) AS SalesTerritoryKey



FROM dbo.FactInternetSales



GROUP BY SalesOrderNumber;
```

Adventure Works is far too small for the timings to mean anything, so treat it as a place to get the logic and the validation right. Then take the pattern to a fact table where it matters.

Which raises the fair question of how much this actually wins, and you can answer that on your own model without benchmarking anything. The saving tracks the reduction in rows reaching the distinct count. If `RowsInMultiLine` comes back as thirty percent of your fact table, then the queries that still need a genuine distinct count are working on thirty percent of the data, and the queries filtered only by safe dimensions are not doing one at all. Those two numbers, the row reduction and the share of your queries that never touch product, tell you most of what a stopwatch would, and they tell you before you build anything.

## Checking you did not break it

Before you ship this, put both versions side by side and make the model prove they agree.

```dax
Transactions Check =



VAR Baseline  = DISTINCTCOUNT ( FactTransaction[TransactionID] )



VAR Optimised = [Transactions]



RETURN



IF ( Baseline <> Optimised, "MISMATCH " & Baseline & " vs " & Optimised, "OK" )

```

Drop that into a matrix and drag every dimension you have through it. Date, store, geography, product, promotion, customer, and combinations of them. Every cell should say OK. Any cell that does not is telling you your routing guard has a hole in it.

Keep the measure in the model while you are developing. Delete it before you publish.

## Where this does not help

Be honest about the cases where this is the wrong tool.

**When almost every transaction is multi line.** A wholesale or B2B model where the average order has forty lines has almost nothing in the single line bucket. You will add ETL complexity and a maintenance burden for a saving in the noise. The distribution query above tells you this in about ten seconds, so run it first.

**When the ID has no functional dependency on anything.** The grain table only exists because a transaction happens at one place at one time. Distinct customers has no such property. A customer shops on many dates at many stores, so there is no set of keys you can pre-aggregate to. The single line split may still help a little, the grain table cannot exist at all.

**When cardinality is already low.** `DISTINCTCOUNT` over a few thousand values is fine. This is a technique for millions of distinct values. Do not carry the complexity if you do not have the problem.

**When you need many different distinct counts.** One grain table serves one ID. If you also need distinct baskets, distinct customers and distinct promotions, you are looking at a table each, and the model starts to sprawl.

**When you cannot change the ETL.** Both halves of this need a build step. If you cannot add a column and a table upstream, you cannot do this.

## Things that will bite you

**Transaction IDs that are not globally unique.** This is the big one, and it is very common in retail source systems. Tills frequently number transactions per store, or reset the sequence daily, so store 12 and store 44 both have a transaction 1001. If that is your data, then `GROUP BY TransactionID` merges two different transactions into one row and your grain table undercounts. Group by the real composite key instead, store plus date plus transaction number, and make sure the DAX measure distinct counts the same composite. If your model has a concatenated key column already, use that.

**Returns and adjustments posted later.** If a return is written back against the original transaction ID but carries the date it was processed, then that transaction now has two dates and the functional dependency is broken. The validation query catches it. Decide deliberately whether a return is part of the original transaction or a transaction of its own.

**Late arriving lines and incremental refresh.** The single line flag is a precomputed fact about a whole transaction, stored on every row of it. If a second line arrives for a transaction that was previously single line, then *every* row of that transaction needs its flag flipped. If your incremental refresh partitions by date and the late line lands in the same partition, you are fine, because the partition rebuilds. If it can land in a different partition from the original, you have a correctness problem that will not announce itself. Work out which of those you are before you rely on this.

**The routing guard is an allowlist, and allowlists rot.** `GrainIsSafe` has to name every dimension that is not represented in the grain table. Add a new dimension to the model in six months, forget to add it to the guard, and queries filtered by it will quietly take the fast path and return the wrong number. This is the highest ongoing risk in the whole pattern. Comment the measure, and keep the check measure handy.

**Bidirectional relationships weaken the test.** `ISCROSSFILTERED` can return true through a bidirectional relationship in cases you did not intend. That is the safe direction to fail, you lose the optimisation rather than the correctness, but it can quietly make the fast path never fire. If your fast path never seems to trigger, look at your cross filter directions first.

**Hide everything.** The flag column and the grain table are implementation details. If a user drags the flag into a slicer, the `CALCULATE` filters in the measure will override it and the result will confuse everyone involved.

## The shape of the idea

Strip away the retail specifics and the pattern is this.

A distinct count is expensive because the engine cannot combine partial results by adding them. Anywhere you can prove that a group of rows collapses to exactly one entity, you can count rows instead of counting distinct values, and counting rows is something a columnar engine is extraordinarily good at.

So the work is not really DAX work. It is finding out which parts of your data have that property. The transaction grain table is that idea applied to whole dimensions. The single line flag is the same idea applied to individual rows. Both come from asking the same question, which is: for this slice of the data, does one row already mean one thing.

Where the answer is yes, stop paying for a distinct count.

---

*Adventure Works is used here because everyone can get it. The technique is aimed at fact tables in the hundreds of millions of rows, where the difference between a row count and a distinct count stops being academic.*

5
1
vote

Article Rating
