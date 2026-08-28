---
title: "Direct Lake Performance Gets a Boost: Faster Join Index Creation"
url: "https://dax.tips/2025/10/20/direct-lake-performance-gets-a-boost-faster-join-index-creation"
published: "2025-10-19"
updated: "2025-10-20"
source: "dax.tips"
---

![](https://i0.wp.com/dax.tips/wp-content/uploads/2025/10/image.png?resize=1000%2C750&ssl=1)

If you’ve been working with **Direct Lake** in Microsoft Fabric, you’ll know its magic resides in its ability to quickly load data. It loads data into semantic models from OneLake when needed. This feature eliminates the overhead of importing. But until recently, the first query on a cold cache might feel sluggish. Why? One reason for this is that Direct Lake must build a join index. This index is added to the model during the first query. This index is a critical structure that maps relationships between tables for efficient lookups.

Earlier, this process was single-threaded and slow, especially on large tables with high cardinality. The good news? That’s changed.

## What’s Improved

The engineering team has introduced **parallelism** and algorithmic optimisations to the join index build process. Instead of scanning and mapping in a linear fashion, the work is now distributed across multiple CPU cores. This means the join index—essentially a hash table that enables constant-time lookups—is created much faster during that first query.

## What is a Join Index?

Think of a join index as a **shortcut map** between two tables. For example:

- You have a **Foreign Key (FK)** table and a **Primary Key (PK)** table.
- When you run a query that joins these tables, the engine needs to find matching rows.
- Without a join index, it scans the PK table repeatedly—slow and expensive.
- With a join index, it uses a pre-built mapping like a hash table. This allows it to jump straight to the right row in constant time.

In **import models**, this index is built during refresh. In **Direct Lake**, it’s built dynamically at query time because data is paged in on demand. That’s why speed matters.

## Why It Matters

- **Lower Latency on First Query:** When data is paged in from OneLake, the join index is built quickly. This action reduces the cold-start penalty.
- **Better Experience for Large Models:** The bigger the tables and the higher the cardinality, the more noticeable the improvement.
- **Subsequent Queries Fly:** Once the join index is built, queries behave much like an import model—fast and predictable.

## Before vs After

Here’s what the improvement looks like in practice:

|  |  |  |
| --- | --- | --- |
| Scenario | Before Optimisation | After Optimisation |
| Small Model (few rows) | ~1.2 sec | ~0.8 sec |
| Large Model (high cardinality) | ~8 sec | ~2 sec |

*(Figures are illustrative based on internal benchmarks—your mileage may vary.)*

## Under the Hood

- **Parallel Build:** The join index creation now uses multiple CPU cores instead of a single-threaded approach.
- **Algorithm Tweaks:** Extra optimisations make sure the process scales better with large datasets.

## What’s Next?

This improvement is already live in production. If you’re using Direct Lake today, you’re benefiting from these changes. More work is underway to tackle other performance bottlenecks, so stay tuned for future updates.

## Call to Action

Try it out! Have you noticed faster query performance on your Direct Lake models? Please share your experience in the comments or on social media. And if you have scenarios where performance still lags, please let me know.

4.6
7
votes

Article Rating
