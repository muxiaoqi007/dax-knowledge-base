---
title: "Dynamic distances in Power BI"
url: "https://dax.tips/2017/05/13/dynamic-distances-in-power-bi"
published: "2017-05-13"
updated: "2017-05-23"
source: "dax.tips"
---

I recently encountered a question posed to the Power BI community forum I found interesting enough to kick-start my blogging on Power BI.

The essence of the question was asking how to dynamically determine distances between two geographic points from user based selections.  So I thought I would cover how this can be done in Power BI to show the closest *N* cities based on straight line distance.

Firstly, I downloaded a list of cities from <http://simplemaps.com/data/world-cities>.  The free file contained approximately 7000 cities with lat/long info.  The data also carried country and province info which may be useful for further analytics.

The data-set looked like this:

![dataset](https://i0.wp.com/dax.tips/wp-content/uploads/2017/05/dataset.png?resize=919%2C406&ssl=1)

I was primarily interested in the city, country, lat & lng columns.

After importing to Power BI I renamed the table ‘**To Cities**‘ and then made two calculated tables based on this.

The first table is to generate a list of cities and countries to use as my origin or from table.  I named this table ‘**From City**‘ and used the following DAX to create the table.  This is the table I would use to allow users to select an origin.

```
From City = SUMMARIZECOLUMNS(
    'To Cities'[country],
    'To Cities'[To City],
    'To Cities'[lat],
    'To Cities'[lng]
    )
```

The second table was simpler and only contains a list of countries.  I would use this in a country slicer to filter down to both the From and To city tables.

```
Countries = SUMMARIZECOLUMNS('To Cities'[country])
```

I then created relationships between the three tables as follows:

![B1 relationships](https://i0.wp.com/dax.tips/wp-content/uploads/2017/05/b1-relationships.png?resize=643%2C526&ssl=1)

Both city tables are connected to the Countries table, but importantly there is no relationship between ‘**To Cities**‘ and ‘**From City**‘ tables.  This is because I do not want either table to act as a filter on the other.

For each Lat/Lng field I made sure I set the Data Category to an appropriate setting as well as specified that the default summarization should be ‘Don’t Summarize’

![B1 Categorisation](https://i0.wp.com/dax.tips/wp-content/uploads/2017/05/b1-categorisation.png?resize=510%2C107&ssl=1)

Now I had my data and my relationships in place, I needed a calculated measure to dynamically derive the distance between cities.  I did some internet searching for an algorithm to determine the distance between two pairs of lat/long points. I found a few versions but not all could be easily converted to DAX using the existing math functions.

Fortunately I found one which I used in the following calculated measure :

```dax
Kilometers =
var Lat1 = MIN('From City'[lat])
var Lng1 = MIN('From City'[lng])

var Lat2 = MIN('To Cities'[lat])
var Lng2 = MIN('To Cities'[lng])
---- Algorithm here -----
var P = DIVIDE( PI(), 180 )
var A = 0.5 - COS((Lat2-Lat1) * p)/2 + 
    COS(Lat1 * p) * COS(lat2 * P) * (1-COS((Lng2- Lng1) * p))/2
var final = 12742 * ASIN((SQRT(A)))
return final

```

I won’t pretend to follow the maths, but I did find the same formula repeated in different places and after a little reconciliation using Google Earth and its distance measurement tool I was happy it was good.

The result is kilometers but can be easily tweaked to return the distance in miles.

With the measure added to my ‘**To Cities**‘ table, I could build a simple report page.

I added two slicers to allow end users to filter on Country and From City which determine the origin city to be used in the **[Kilometers]** calculated measure from above.

Then I added a matrix and map visual to the report canvas.

The Matrix visual I configured as follows.  The main point here is that the ‘To City’ column is using a Top N filter which I set to use the bottom 10 values based on the kilometers measure.  I used Bottom so that it would filter the closest cities.

![B1 Matrix](https://i0.wp.com/dax.tips/wp-content/uploads/2017/05/b1-matrix.png?resize=374%2C836&ssl=1)

For the Map visual I configured as follows, again using the Top N visual level filter to help find the closest 10 cities.

![B1 map](https://i0.wp.com/dax.tips/wp-content/uploads/2017/05/b1-map.png?resize=366%2C631&ssl=1)

My very basic report finished up like this, but allows me to choose a country and city from the two drop-down slicers and the matrix and map would show me the closest 10 cities.  The matrix is sorted using the Kilometers column and I also added the distance measure to the tool tip on the map.

![B1 finshed report](https://i0.wp.com/dax.tips/wp-content/uploads/2017/05/b1-finshed-report.png?resize=935%2C538&ssl=1)

This approach can easily be tweaked to allow for a number of use cases and I hope my blog debut proves to be of use in some way.

Here is a link to the PBIX file.  The model itself is very plain but hopefully useful

<https://1drv.ms/u/s!AtDlC2rep7a-kBygBveM-q8V8fh->

Please feel free to comment or feedback so I can improve for future blog posts.

5
2
votes

Article Rating
