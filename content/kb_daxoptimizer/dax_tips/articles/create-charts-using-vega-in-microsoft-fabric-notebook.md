---
title: "Create charts using Vega in Microsoft Fabric Notebook"
url: "https://dax.tips/2023/12/05/create-charts-using-vega-in-microsoft-fabric-notebook"
published: "2023-12-05"
updated: "2023-12-05"
source: "dax.tips"
---

I recently needed to generate a quick visual inside a Microsoft Fabric notebook. After a little internet searching, I found there are many good quality charting libraries in Python, however it was going to take too long to figure out how to create a very specific type of chart.

This is where [Vega](https://vega.github.io/) came to the rescue. The purpose of this short article is to share a very simple implementation of generating a Vega chart using a Microsoft Fabric notebook.

The example below assigns a public dataset to a dataframe at line 2. You can easily assign your own data to the dataframe and update the Vega spec to match columns in the dataframe.

```dax
import pandas as pd
df = pd.read_json('https://vega.github.io/vega/data/cars.json', orient='values')

##Define Spec
spec="""{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "data": { "values": """ + df.to_json(orient="records") + """ },
  "mark": "point",
  "encoding": {
    "x": {"field": "Horsepower", "type": "quantitative"},
    "y": {"field": "Miles_per_Gallon", "type": "quantitative"}
  }
}"""

##Display Chart
displayHTML(
    """
    <!DOCTYPE html>
    <html>
        <head>
            <script src="https://cdn.jsdelivr.net/npm/vega@5"></script>
            <script src="https://cdn.jsdelivr.net/npm/vega-lite@5"></script>
            <script src="https://cdn.jsdelivr.net/npm/vega-embed@6"></script>
        </head>
        <body>
            <div id="vis"></div>
            <script type="text/javascript">
                var spec = """ + spec + """;
                var opt = {"renderer": "canvas", "actions": false};
                vegaEmbed("#vis", spec, opt);
            </script>
        </body>
    </html>"""
 )

```

By copying the above text and running in a code cell, you should get the following result. The spec can be extracted and copied into the [Vega editor](https://vega.github.io/editor/) for further refinement and then updated back to the code cell.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2023/12/image-4.png?resize=645%2C712&ssl=1)

5
2
votes

Article Rating
