---
title: "Use Bookmarks to lazy-load Power BI Visuals"
url: "https://dax.tips/2019/12/24/use-bookmarks-to-lazyload-visuals"
published: "2019-12-23"
updated: "2019-12-24"
source: "dax.tips"
---

When Power BI loads a report page, it fires a query for every *visible* visual on the page. For some pages, this can result in a large number of queries all fired simultaneously at the underlying database. This load can cause quite a bit of contention, and in some cases, will slow down the overall load time of the page.

One option to help smooth the work of loading visuals is to make use of Bookmarks in Power BI. The key to this tip is hidden visuals won’t fire a query to retrieve data *until* they are made visible. This technique opens the door to layering the loading process of visuals in some report pages to be controlled by the end-user.

The idea is to hide specific visuals on a given page, so they don’t try to load at the point the page is initially loaded; but provide the end-user with a button they can click when *they* are ready to load the hidden visual.

Lazy loading visuals

The above video shows the performance analyser tool starts recording before loading the **By Manufacturer** tab. The Performance Analyser panel shows a set of individual timings for all the non-hidden visuals rendered at the point the page opens. Then, when each of the two buttons are clicked, the Performance Analyser shows the queries executed for each visual in the panel.

To apply this to a report page,

1. Start by hiding all the visuals you want to lazy load
2. Add as many buttons as needed to trigger the reveal of a set of visuals
3. Save the report
4. Unhide all the visuals you want to reveal for a specific bookmark
5. Hide the button you plan to use to reveal the visuals at step 4.
6. Create the bookmark (only set the bookmark to update specific visuals)
7. Repeat from step 4 as needed

This approach will not only allow all the non-hidden visuals to load faster when the page opens but also means when the user clicks a button to reveal a hidden visual, there will be less contention in the system for the lazy loaded visuals.

Some ideas for this approach could be to:

- Hide one particular heavy query until slicers get set, then load the visual.
- Hide groupings of visuals that may only be needed occasionally for analysis.
- Hide visuals that might exist lower in a page that requires scrolling

It’s not ideal to require additional clicks by an end-user to lazy load the additional visuals – but for some scenarios, this unlocks a performance gain that could improve some reports. Power BI is an interactive tool where the end-user is used to clicking on slicers and inside elements of various visuals – so perhaps it might be OK with your end-users.

A copy of a PBIX file showing this technique is available to [download here](https://1drv.ms/u/s!AtDlC2rep7a-yH6D-ONiqADtt_ys?e=legFSb).

5
2
votes

Article Rating
