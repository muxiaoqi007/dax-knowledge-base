---
title: "Visualize Power BI Refresh using Semantic-link"
url: "https://dax.tips/2023/12/05/visualize-power-bi-refresh-using-sempy"
published: "2023-12-05"
updated: "2023-12-05"
source: "dax.tips"
---

A few blogs back I shared a technique using Power BI Profiler (or VS Code) to run and capture a trace over a refresh of a Power BI semantic model (the object formally known as a dataset).

I’ve since received a lot of positive feedback from people saying how useful it was to visualize each internal step within a problematic Power BI refresh. Naturally, in the age of Fabric, I’m keen to share how the same approach works using a Microsoft Fabric Notebook.

Fortunately, it’s easy thanks to some recent features added to the [semantic-link](https://pypi.org/project/semantic-link-sempy/) Python library. The steps shown in this article represent the code you might add to individual code-cells. However, these could happily be tailored to work in a single code cell if you prefer.

Once complete, you should be able to see a Gannt style chart similar to the following to help understand more about your Power BI refresh.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2023/12/image-1.png?resize=1000%2C569&ssl=1)

The approach still primarily focuses on import mode semantic models, rather than any using Direct Lake,

As an added bonus, the notebook uses [vega-lite](https://vega.github.io/vega-lite/) to generate a nice easily customizable Gantt chart. This will particularly appeal to readers who also enjoy using Deneb visuals in Power BI who are used to Vega specs.

Highlights of this blog show:

- Using semantic-link to trace a semantic model.
- Using semantic-link to kick-off a Power BI refresh.
- Using vega-lite to create visual of the trace results.

The following headings represent individual code-cells within a new, or existing Fabric notebook.

## Install semantic-link

Create a new notebook in Fabric. This does not have to be in the same workspace as the semantic model you would like to analyse. When this block runs, you should see a version of 0.4.0 or higher.

```
%pip install semantic-link
```

## Define Event Trace Schema

Define the columns and events to be included in the trace once it gets run.

import sempy.fabric as fabric

```
import sempy.fabric as fabric
import pandas as pd
import time
import warnings

base_cols = ["EventClass", "EventSubclass", "CurrentTime", "TextData"]
begin_cols = base_cols + ["StartTime"]
end_cols = base_cols + ["StartTime", "EndTime", "Duration", "CpuTime", "Success","IntegerData","ObjectName"]

# define events to trace and their corresponding columns
event_schema = {
    "JobGraph": base_cols ,
    "ProgressReportEnd": end_cols
}

warnings.filterwarnings("ignore")
```

## Start trace and run refresh

The next step creates the trace at lines 1 and 3, while the trace gets started at line 5.

The refresh gets kicked off at line 8 using the recently released enhanced refresh API. The name of both the semantic model and workspace get passed to the **fabric.refresh\_dataset** method. The refresh process is asynchronous, so the next few lines iterate every two seconds checking the status of the Power BI refresh.

There is a neat trick at line 19 to show a progress bar using the Python **Print()** function underneath the code block.

Once the refresh completes, the trace gets stopped at line 30, with the results stored in a dataframe called final\_trace\_logs. The contents of the final\_trace\_logs dataframe will look familiar to anyone who has run a profiler trace in the past.

```
with fabric.create_trace_connection("Trace Refresh Dataset") as trace_connection:
    # create trace on server with specified events
    with trace_connection.create_trace(event_schema, "Simple Refresh Trace") as trace:

        trace.start()
        
        ## RUN THE REFRESH HERE
        request_status_id = fabric.refresh_dataset("Trace Refresh Dataset", "Delta Analyzer", refresh_type="full", max_parallelism=3, retry_count=1)

        print("Progress:", end="")

        while True:

            status = fabric.get_refresh_execution_details("Trace Refresh Dataset", request_status_id).status

            if status == "Completed":
                break

            print("░", end="")

            time.sleep(2)


        print(": refresh complete")

        # allow ending events to collect
        time.sleep(5)

        # stop Trace and collect logs
        final_trace_logs = trace.stop()

final_trace_logs = final_trace_logs[final_trace_logs['EventSubclass'].isin(["ExecuteSql","Process"])]
display(final_trace_logs)
```

![](https://i0.wp.com/dax.tips/wp-content/uploads/2023/12/image.png?resize=1000%2C399&ssl=1)

## Define Vega-lite HTML host

The next step is two create a simple HTML document to host the chart. The HTML document includes references to Vega client script at lines 6, 7 & 8. These scripts carry the code for the **vegaEmbed** function at line 32. I hope you recognize how easy it is to customize and style a presentation layer around the final visual that ultimately renders in the <div /> block at line 25.

```dax
def showChart (spec):
    h = """
    <!DOCTYPE html>
    <html>
        <head>
            <script src="https://cdn.jsdelivr.net/npm/vega@5"></script>
            <script src="https://cdn.jsdelivr.net/npm/vega-lite@5"></script>
            <script src="https://cdn.jsdelivr.net/npm/vega-embed@6"></script>
            <style>
                table, th, td {
                border: 10px solid #e7e9eb;
                border-collapse: collapse;
                }
            </style>
        </head>
        <body>
            <table>
                <tr>
                    <td style="text-align: center;">
                        <h1>Refresh Results</h1>
                    </td>
                </tr>
                <tr>
                    <td>
                        <div id="vis"></div>
                    </td>
                </tr>
            </table>    
            <script type="text/javascript">
                var spec = """ + spec + """;
                var opt = {"renderer": "canvas", "actions": false};
                vegaEmbed("#vis", spec, opt);
            </script>
        </body>
    </html>"""

    displayHTML(h)

```

## Prepare trace dataframe for visual

A little post-processing is required to be applied to the trace data-frame to prepare it for a visual. The most important feature of this step is to convert data-time values to numeric (millisecond) values, with an offset to base everything to zero.

This step adds two columns called **Start** and **End** to the final\_trace\_logs dataframe.

```
import numpy as np
#final_trace_logs.dtypes
df=final_trace_logs
df["Start"] = df['StartTime'].astype(np.int64) / int(1e6)
df["End"]   = df['EndTime'].astype(np.int64) / int(1e6)
Offset      = min(df["Start"] ) 
df["Start"] = df["Start"] - Offset
df["End"]   = df["End"] - Offset
display(df)
```

## Create Gantt chart using Vega spec

The last step creates a string that includes a Vega spec to be rendered. The dataframe captured from the trace gets injected at line 5. I’ve tested with different types of datasets and visuals, and it seems you can render using some pretty large datasets. I stopped testing at 100k rows, and found charts still rendered ok on my very old laptop.

I highly recommend the [Vega editor](https://vega.github.io/editor/) to help with syntax and customizing your Vega chart. The spec here includes tooltips and a few other hints to get you on your way. I wanted to generate a visual similar to a previous blog and was impressed how flexible Vega can be.

```
spec= """{
        "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
        "description": "A simple bar chart with ranged data (aka Gantt Chart).",
        "data": { "values": """ + df.to_json(orient="records") + """ },
        "width" : 800 ,
        "height" : 400 ,
        "mark": "bar",
        "encoding": {
            "y": {
                "field": "ObjectName", 
                "type": "ordinal",
                "axis" : {
                     "labelFontSize": 15 ,
                     "titleFontSize": 20 ,
                     "title" : "Object"
                    }
                },
            "x": {
                    "field": "Start", 
                    "type": "quantitative" , 
                    "title" : "milliseconds",
                    "titleFontSize":20 ,
                    "axis" : {
                        "titleFontSize": 20
                        }
                    },
            "x2": {"field": "End"},


            "color": {
                "field": "EventSubclass",
                "scale": {
                    "domain": ["Process", "ExecuteSql"],
                    "range": ["#FFC000","#0070C0" ]
                    } ,
                "legend": {
                    "labelFontSize":20 ,
                    "titleFontSize":20 ,
                    "title":"Event Type"
                    }
                },

            "tooltip": [
                    {"field": "Duration", "type": "quantitative","format":","} ,
                    {"field": "CpuTime", "type": "quantitative","format":","} ,
                    {"field": "EventSubclass", "type": "nominal"} 
                    ]
            }
        }"""
showChart(spec)
```

![](https://i0.wp.com/dax.tips/wp-content/uploads/2023/12/image-1.png?resize=1000%2C569&ssl=1)

## Summary

In this article I share three neat tricks that you can to today with a Microsoft Fabric notebook. There is no need for using third party tools to help you:

- Run a Trace
- Kick off a Power BI Refresh
- Use the Vega-lite grammar to generate interesting visuals.

Hopefully, this inspires you to copy and customize elements in a number of different ways. Please feel free to modify or tweak the code to suit your own requirements. I’d love to hear different ways you have implemented parts of these tips.

My next blog will show how to run a trace over a single DAX query and analyze the results in a similar way. Great for debugging long running DAX queries.

You can download a version of this notebook from [dax-tips/semantic-link (github.com)](https://github.com/dax-tips/semantic-link).

4.9
9
votes

Article Rating
