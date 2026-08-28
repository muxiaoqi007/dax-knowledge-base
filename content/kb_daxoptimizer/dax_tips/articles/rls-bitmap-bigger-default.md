---
title: "Why the RLS bitmap default just went up 4x"
url: "https://dax.tips/2026/06/11/rls-bitmap-bigger-default"
published: "2026-06-11"
updated: "2026-06-11"
source: "dax.tips"
---

If you have ever watched a model with row-level security run beautifully for months and then quietly fall off a cliff, this one is for you. Nothing in the model changed. You did not touch the security rules. One table just grew past a line you could not see, and every query under that role started doing a great deal more work than it used to.

That line just moved, and it moved in your favour. Here is what it is and why it sits where it does.

## What row-level security actually does

Row-level security in a Tabular model is a DAX expression attached to a table inside a role. The engine runs that expression, the *predicate*, against every row of the table. Rows where it returns `TRUE` are visible to members of the role. Everything else disappears, not just from the report but from totals, from `COUNTROWS`, from everything downstream.

Predicates come in two flavours, and the engine cares about the difference a lot more than you do.

```powerquery
-- Cheap. A column compared to a constant or a user attribute.



Geography[Country] = "Canada"



-- Expensive. Needs a lookup, a relationship traversal, a measure.



Customer[CustomerID] IN



CALCULATETABLE ( VALUES ( Access[CustomerID] ), Access[User] = USERPRINCIPALNAME() )

```

Both answer the same yes or no question for each row. The cheap one can be answered inside the columnar scan without leaving the storage engine. The expensive one cannot, and that is where things get interesting.

## Three ways to apply a filter

When a query runs under an active role, the engine picks one of three strategies.

```
                    Is the predicate simple
                    enough to push into the scan?
                              |
                  yes ────────┴──────── no
                   |                     |
          1. Native filter      Is the table small enough
          (free, in the scan)   to build a bitmap for?
                                         |
                             yes ────────┴──────── no
                              |                     |
                     2. Cached bitmap      3. Per-row callback
                     (build once, reuse)   (call back for every row)
```

Path one is free. Path three is the slow one Marco Russo has been warning people about for years in [his piece on the cost of security in Tabular](https://www.sqlbi.com/articles/security-cost-in-analysis-services-tabular/). Path two, the bitmap, is the one that just got more room to breathe.

## The bitmap, in one picture

When a predicate is too complex to push into the scan, the engine evaluates it once and writes down the answer as a bitmap. One bit per row. One means the row is visible to the role, zero means it is filtered out.

```
Predicate:  Geography[Country] = "Canada"



Row#:    0    1    2    3    4    5    6    7



Country: CA   US   CA   CA   JP   US   CA   CA



Bit:     1    0    1    1    0    0    1    1      1 = visible, 0 = filtered
```

Under the covers it is a single contiguous bit array, sliced to line up with the table’s storage segments and packed into 64-bit words so the engine can march through 64 rows at a time. At query time it does not re-run your DAX. It just ANDs that bitmap into the same scan it was already going to do. One pass, no detours back into the formula engine.

A few things worth knowing about it:

- It is built for the rows of the table the predicate sits on, usually a **dimension** table, and it is probed while the engine scans the **fact** table through the relationship.
- It is built **lazily**, on the first query under that role. The first person through the door pays for it once. Everyone after that gets it for free.
- How widely it is **shared** depends on the predicate. A static predicate, one whose answer does not depend on who is asking, produces a single bitmap that every user in the role shares. A dynamic predicate, one that calls `USERNAME` or `USERPRINCIPALNAME`, gives a different answer per user, so each user gets their own bitmap. The same user’s queries still reuse it; two different users in the same role do not.
- It **lasts until the next write or eviction**. Any committed write transaction against the model, a refresh, an `ALTER`, a schema change, clears it. So does the model being evicted from memory or loaded up on another node, whichever happens first. Short of that it sticks around, and adding or removing users in the role does not throw it away.
- There is one bitmap per table per role, so a model with several roles builds several bitmaps, and a role using dynamic security builds one per user on top of that.

## The change: 128K becomes 512K

Here is the actual news. The engine only builds and caches a bitmap up to a certain table size. Above that size it gives up on the bitmap and drops to the per-row callback, which is the path you do not want.

|  | Threshold | Bitmap size |
| --- | --- | --- |
| Old default | 128K rows | 16 KiB |
| New default | 512K rows | 64 KiB |

That is a 4x increase, and it arrives with the engine. You do not change anything in your model. Any table sitting in the 128K to 512K band with a complex security predicate now stays on the fast bitmap path instead of falling back to callbacks.

## Why 512K and not two million

This is the part I found genuinely good. The first instinct was to push the threshold much higher, out towards one or two million rows. The number landed at 512K because of how a CPU is built, not because of anything in the data.

A 512K-row bitmap is 64 KiB. That fits comfortably inside the cache that sits right next to a modern CPU core, which is where you want it if you are going to AND it into a scan over and over. Make the bitmap much bigger and it stops fitting in that cache, and you start paying to fetch it from further away every time you use it.

To be honest about it, bigger would still usually win. Even at one or two million rows, a cached bitmap beats calling back into the formula engine for every single row, because that callback is so expensive. So this is not a hard cliff where 512K is fast and 513K is a disaster. It is a pragmatic sweet spot: big enough to catch a lot more real-world dimension tables, small enough to stay where the CPU likes it.

## What this means for your models

The guidance has not changed, you just got more headroom.

- Keep security predicates simple where you can. A simple predicate never leaves the scan and none of this applies to it.
- If you have a complex predicate sitting on a large table, look at whether you can \*\*normalise it\*\*. Move the predicate off the big fact table and onto a smaller related dimension table that sits under the threshold. This is ordinary star-schema discipline, and row-level security is one more reason to follow it.
- A clean star schema wins twice here. Most of your dimension tables were already under the old 128K line, and the new 512K line gives the borderline ones some breathing room.

## Credits

The fix was done by **Ming Han Teh** on the Analysis Services engine team. **Sean Tang** and **Marius Dumitru** walked me through the architecture and the reasoning behind the number, which is the only reason the cache explanation above is more than hand-waving. Thanks all three.

If you have a model where a security predicate sits on a table somewhere between 128K and 512K rows, this is the rare performance improvement you get for doing nothing at all.

5
2
votes

Article Rating
