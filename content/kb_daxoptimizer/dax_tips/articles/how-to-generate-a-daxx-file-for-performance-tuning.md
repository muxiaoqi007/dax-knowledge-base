---
title: "How to Generate a DAXX File for Performance Tuning"
url: "https://dax.tips/2025/10/31/how-to-generate-a-daxx-file-for-performance-tuning"
published: "2025-10-30"
updated: "2026-04-21"
source: "dax.tips"
---

![](https://i0.wp.com/dax.tips/wp-content/uploads/2025/10/troubleshooting-a-dax-query.png?resize=1000%2C750&ssl=1)

When troubleshooting a slow DAX query, the right diagnostic bundle is worth a hundred screenshots. That bundle is a **DAXX file**, generated from DAX Studio. It captures the query, the server timings, the query plan, and a VertiPaq Analyzer snapshot of your model, all in one package, and without any of your actual query results. Perfect for sending to an expert (or an AI agent) for review.

## What is a DAXX File?

A DAXX file is a diagnostic package created by DAX Studio. It is technically a ZIP archive containing:

- **Query.txt** — the DAX expression you ran
- **ServerTimings.json** — storage engine and formula engine events, timings, CPU
- **QueryPlan.json** — the logical and physical query plan
- **Model.vpax** — a VertiPaq Analyzer snapshot (row counts, cardinality, relationships, measures)

It does **not** contain your query results, so sensitive data stays out of the file.

## Don’t confuse **.dax** and **.daxx**

DAX Studio has two save options that sound almost identical but are completely different:

| Extension | What it is | Typical size |
| --- | --- | --- |
| **.dax** | Plain text file containing only the query | A few bytes to a few KB |
| **.daxx** | Full diagnostic bundle (ZIP) | Hundreds of KB to several MB |

If the file you saved is only a few bytes, you almost certainly saved a .dax, not a .daxx. Go back and pick the right option from the Save As dropdown. This is by far the most common mistake I see.

## When Should You Create a DAXX File?

Any time a DAX query is slower than you expect. The usual path is:

- A report visual is slow
- You capture the DAX behind it with Performance Analyzer
- You reproduce the slowness in DAX Studio
- You save the session as DAXX and send it for review

## Prerequisites

- **DAX Studio** installed. Download: <https://daxstudio.org/>
- A recent version that supports the DAXX export.
- A data source to connect to: Power BI Desktop (auto-detected), Power BI Service (via XMLA endpoint), or SSAS/AAS.

## Step 0: Get the Slow Query from Power BI Desktop

Most DAXX problems start here because people hand-copy the wrong thing. The right way:

- In Power BI Desktop, open the report with the slow visual.
- Go to **View → Performance Analyzer**.
- Click **Start recording**.
- Interact with the visual (slicer change, page load, whatever is slow).
- Click **Stop**.
- Expand the slow row. You will see a **Copy query** link. **Click it.**

That copies the exact DAX expression the engine ran, parameters and all. Paste it straight into DAX Studio. Do not retype it, do not trim it, do not hand-edit the filters. The copy is the truth.

## Steps to Generate a DAXX File in DAX Studio

1. **Connect.** Open DAX Studio. It should auto-detect any running Power BI Desktop instance. Otherwise, connect to the XMLA endpoint or Analysis Services server.
2. **Paste the query** you copied from Performance Analyzer into the editor.
3. **Enable the traces** on the Home ribbon before you run anything:
   - **Server Timings** — storage engine events, scans, durations
   - **Query Plan** — logical and physical plans
   - **Clear on Run** — resets earlier traces so you only capture this run

![](https://i0.wp.com/dax.tips/wp-content/uploads/2025/10/daxstudio1.png?resize=1000%2C125&ssl=1)

4. **Capture VPAX.** Go to **Advanced → Export VPAX**. This snapshots the model metadata and statistics into the session so they get bundled when you save.   
     
   Do not skip this step. Without it, the DAXX file will be missing the VPAX portion and a reviewer loses half the picture.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2025/10/daxstudio2.jpg?resize=396%2C174&ssl=1)

5. **Run the Query**:
   - **Run the query** (F5). Let it complete. Check that the Server Timings pane has populated with events. If it is empty, the trace was not active when the query ran, and the DAXX will be missing the most important part.
6. **Save as DAXX**:
   - **File → Save As**
   - In the Save As dialog, **change the file type dropdown** from DAX (.dax) to DAX Studio Package *(*.daxx)
   - Save it somewhere you can find it

The extension matters. The default save is often .dax. You want .daxx.

## Verify You Did It Right

Before you send the file, check all three of these:

- **File size** is more than **100 KB**. A bare .dax is a few bytes. A real .daxx is hundreds of KB minimum.
- **Right-click the file → Open with 7-Zip** (or rename a copy to `.zip` and open). You should see `Query.txt`, `ServerTimings.json`, and `Model.vpax` inside.
- The .vpax is not empty. It is itself a ZIP containing Model.bim, DaxModel.json, and DaxVpaView.json.

If the file is too small, or the contents are missing, go back and save again.

## Common Mistakes

These account for almost every bad DAXX file I receive:

- **Saved .dax instead of .daxx.** The dropdown defaults to .dax. Pick the other one.
- **Query never ran with traces on.** Enable Server Timings and Query Plan *first*, then run. The traces only capture live events.
- **Pasted a single field or measure reference instead of the whole query.** A bare [Measure Name] is not a query. Use Performance Analyzer’s **Copy query** button to grab the full expression.
- **Cleared traces or closed the Server Timings pane before saving.** Keep everything open. Save immediately after a fresh run.
- **Forgot to export VPAX.** Run **Advanced → Export VPAX** before you save. Without it the DAXX is missing the model snapshot and a reviewer cannot see your model structure.
- **Sent the wrong slow query.** The one that felt slow in the report is not always the one the engine is spending time on. Use Performance Analyzer to pick the right row.

## Why Share a DAXX File?

A good DAX reviewer (or analyser tool) can use the file to identify:

- Storage engine scans that are doing too much
- Formula engine bottlenecks and callback overhead
- Filter context issues
- High cardinality columns or relationships that need work
- Model structure problems (missing keys, sub-optimal relationships)
- Measure patterns that prevent storage engine parallelism

Without a DAXX, most of that has to be guessed.

## Summary

One query. Three traces enabled. One VPAX export. One save dialog with the right file type picked. A quick sanity check that the file is the right size and contains what it should. That is the whole recipe.

If the file you just saved is only a few bytes, that is a .dax, not a .daxx. Try again. We have all done it.

5
4
votes

Article Rating
