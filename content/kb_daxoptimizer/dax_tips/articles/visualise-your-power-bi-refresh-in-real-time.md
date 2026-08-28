---
title: "Visualise your Power BI refresh – in real-time"
url: "https://dax.tips/2021/05/11/visualise-your-power-bi-refresh-in-real-time"
published: "2021-05-10"
updated: "2021-05-23"
source: "dax.tips"
---

## Introduction

I recently [[visualise-your-power-bi-refresh|wrote an article]] showing how you can visualise a dataset refresh using Power BI. It was a pretty cool way to show some of the internal workings of what otherwise is a black box. The idea from my earlier article uses SSMS Profiler to run a trace against a database hosted in Azure AS, or Power BI Premium. Once the refresh is complete, you import the results of the SSMS Profiler trace into a Power BI report to analyse. The approach requires you to wait until the refresh is complete *before* you can start exploring the data.

Also recently, I had the opportunity work on some large models that took a *long* time to refresh. I wondered what might be required to update the earlier process to study the results while the refresh was underway. Does that make me too impatient? Here is what I ended up building.

For a start, I needed to find a substitute method for running a trace against an Analysis Services database. The SSMS Profiler lets me define a trace, choose events, filters and columns etc.; I need to wait until the refresh is complete before I can use the data. I also need to send Profiler events to Power BI as they occur in a way that it could use in real-time reports.

Unsurprisingly, I opted for a [Visual Studio Code](https://code.visualstudio.com/) and created a small C# console based app which uses the Analysis Services client [T.O.M. libraries](https://docs.microsoft.com/en-us/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo?view=asallproducts-allversions) to run the same Profiler Trace that SSMS Profiler would. The console app can also easily handle posting events to Power BI in real-time. If this sounds daunting, please bear with me. As always, I will share step by step instructions on how to get this small tool up and running.

The following animated GIF shows a real-time report running in Power BI while I refresh an AdventureWorksDW dataset.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/2021-05-10_15-40-00.gif?resize=1000%2C563&ssl=1)

## High-level summary

There are three main elements required to get this up and running.

1. Create a simple C# console app to run a trace against your model (full code supplied)
   - The model to be traced can be hosted in Azure Analysis Services, or Power BI Premium (needs XMLA-Read)
   - The events to be capture by the trace are:
     - [ProgressReportEnd](https://docs.microsoft.com/en-us/dotnet/api/microsoft.analysisservices.traceeventclass?view=analysisservices-dotnet)
     - [ProgressReportCurrent](https://docs.microsoft.com/en-us/dotnet/api/microsoft.analysisservices.traceeventclass?view=analysisservices-dotnet)
2. Build a Power BI report in Power BI Desktop that connects to the above-shared streaming dataset
   - Use a Gantt chart to show the ProgressReportEnd events (ExecuteSQL and ReadData)
   - Use a line chart to display the speed and progress of each element from the refresh

I have added a [video walk-through](#video-walkthrough) of this at the bottom of the article

[UPDATE: 2021-05-13, I have simplified the instructions from the original version of this article].

## Caveats

Once you publish the Power BI Report used to visualise the report, you need to click the refresh button when viewing the service manually. Even with Automatic Page Refresh (APR) set to refresh the Power BI report every few seconds, Power BI overrides this for streaming datasets. The page will only automatically refresh after 30 mins and not sooner (I know!!), but I have good news.

I wasn’t happy with this page-refresh interval, so I asked around and found the newly announced streaming Dataflows *will* allow APR to refresh every few seconds. The animated GIF at the top of this article uses streaming Dataflows.

You can also ping report object to a dashboard to get the effect of realt-ime streaming.

Streaming Dataflows is not yet been released but does work based on the early internal version. I got the chance to get hands-on with the new feature. Streaming Dataflows use Azure Event Hubs as a source, but the C# app can easily post trace event data as it arrives at an Azure Event Hub. I will show this working in the attached demo.

## Step 1: Create a C# console app

Technically you could build this step several ways, but my preferred option is to use Visual Studio Code. I already have a few articles that show how to build apps to interact with Analysis Services databases using Power BI. [Here is a list](https://dax.tips/?s=visual+studio+code) of some of those articles. One alternative is to use Visual Studio. The community edition is free, but you will need .Net framework client libraries rather than .Net Core.

First off, make sure you have [Visual Studio Code](https://code.visualstudio.com/) installed on your machine. Visual Studio Code is a free, lightweight tool built and managed by Microsoft. Ensure you have the latest [.Net Core SDK](https://dotnet.microsoft.com/download/dotnet/5.0). This project uses version 5.0. Also be sure to have [GIT](https://git-scm.com/downloads) installed on your machine.

In summary, the prerequisites to get up and running are the following free tools. You may already have some of these.

- Install [Visual Studio Code](https://code.visualstudio.com/)
- Install the [.net Core 5.0 SDK](https://dotnet.microsoft.com/download/dotnet/5.0) (not just the runtime)
- Install a [Github Client](https://git-scm.com/downloads)
- Once you have all the above, you can clone a copy of the source code using the instructions below.

The Github link for the source code [is here](https://github.com/dax-tips/TracePost). Please feel very free to use and contribute to this project.

In the command line of your machine create a new folder, move into the new empty folder then use the git clone command to get a copy of the latest version of this code. Change into the TracePost folder and start Visual Studio Code.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B13.jpg?resize=774%2C473&ssl=1)

```
md myProject

cd myProject

git clone https://github.com/dax-tips/TracePost

cd TracePost

Code .
```

Open the program.cs file and click “restore dependancies” when prompted.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/CreateProject.gif?resize=1000%2C563&ssl=1)

### Update Parameters

Once you have the project restored, open and edit the file called **appsettings.json**. This is the file that contains the parameters unique to you.

Update the **databaseToTrace** parameter to show the name of the database you want to trace. This parameter determines the database you want to trace the activities during a Power BI refresh. This parameter is NOT the streaming dataset where events get posted for analysis.

If this is Azure Analysis Services, use the text from the Management Server name as the value. If you want to trace a Power BI report, you will need to include a connection string that specifies both the **Data source** and the **Initial catalog** properties. The data source is the Power BI Premium workspace connection.

```
"Data source=powerbi://api.powerbi.com/v1.0/myorg/Trace Post Demo;Initial catalog=AdventureWorksDW;";
```

Update the **workspaceName** parameter to the folder you would like to automatically create the streaming dataset in the Power BI Web Service. This parameter should look something like the following :

```
"Trace Post Demo"
```

Note, if you type value here that does not exist in your tenant, this code will create the workspace and the streaming dataset.

### Check debug terminal console

Check the launch.json file in the .vscode folder to make sure the “**console**” property is set to “externalTerminal”.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B4-2.jpg?resize=688%2C395&ssl=1)

### Run/debug the app

Press F5 to start debugging your application. If everything goes well, you should get prompted for two logins. The first is for the tenant to create the streaming dataset. The second MFA login screen allows you to connect to the database you want to trace. Once you successfully authenticate, you should see the following console screen.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B5-1.jpg?resize=627%2C360&ssl=1)

That’s it! Not a very exciting screen to look at, but you can tweak or enhance the output to suit. At this point, you have a trace running against your source database. So, any refresh activity taking place now get picked up by the app and events get sent to the Power BI Streaming API as they get generated.

The Queue Count number shows how many unprocessed events have been captured by the trace, but not yet sent to the Streaming dataset. Use this number to help tune the batch size.

The Rows Processed number represents the total number of rows sent to the streaming dataset since the app was started.

Finally, the current time value shows the app hasn’t frozen and should update every 5 seconds.

### Code Structure

I’ve added what I think are helpful comments throughout the program.cs file to explain what is going on. In essence, the idea is first to connect to an Analysis Services database using the TOM client libraries. Then, once authenticated, create a Trace with just two events. The two events are ProgressReportEnd and ProgressReportCurrent (I don’t use JobGraph for this). Each event has a set of columns defined to help capture the bare minimum data we need.

Once the trace is running, the Trace\_OnEvent function receives events as they get generated during the trace. The trace events get added to a [FIFO Queue](https://docs.microsoft.com/en-us/dotnet/api/system.collections.queue?view=net-5.0) to build up to allow events to get sent to the Power BI Streaming Dataset in batches.

The **main()** function has a loop block that checks the queue every 5 seconds to see if it contains any events. If events get found, a JSON string gets generated containing multiple rows/events that then get posted off to the streaming API.

## Step 3: Analysing in Power BI

If you are running the C# console app, you can now trigger a full/partial refresh of the database being traced, and activity should now start to appear in the streaming dataset.

To test if this is working, log into the Power BI web service and create a report from the streaming dataset in the workspace.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B6-1.jpg?resize=562%2C214&ssl=1)

Drag the **CurrentTime** field from the streaming dataset to the canvas and configure it as a KPI visual. Change the field to be a **Count of CurrentTime** in the Fields well of the Visualizations pane.

Once you have kicked off a refresh of the dataset, you can manually refresh the Power BI report to reflect the count of events in the streaming dataset.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B7-1.jpg?resize=687%2C590&ssl=1)

Next, create a Line Chart to show the speed of rows processed for every table/partition in your database. This data comes from the **ProgressEventCurrent** trace event and is a snapshot for every 10,000 rows processed per table/partition.

Drag the **CurrentTime** field to the axis, the **ObjectName** or **LongObjectName** field to the Legend and finally the **IntegerData** field to Values.

Create a filter on this Line Chart visual so that it only shows values where **EventClass** is ProgressReportCurrent.

If you hit the refresh button on the report while the dataset is refreshing, you can see the lines on the Line Chart grow up and head to the left.

The height on the y-axis represents the number of rows processed for each object, so the steepness of each line is a good indicator of how fast each table/partition is refreshing.

Another option is to change the filter (in the Filter Pane) of the CurrentTime visual to only show the most recent 20 mins using the relative time advanced filter setting.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B8-2.jpg?resize=1000%2C348&ssl=1)

For a Gantt chart, I like to import the **Craydec Timelines visual** to plot the ProgressReportEnd events.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B9.jpg?resize=556%2C425&ssl=1)

Once this visual gets imported, drag the **ObjectName** field to Entity, **EventSubClass** to Color, **StartTime** to Start and finally **EndTime** to End on the visual. Set a filter on the EventClass, so the visual only shows ProgressReportEnd events.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B10-1.jpg?resize=840%2C294&ssl=1)

Play around with the position and formatting of the visuals to suit your preferences. Get frustrated and contemplate calling Chris Hamill on Teams to say, “*Help, I’m doing this thing for a blog, and my report layout sucks! Can you help me*” before realising it’s the weekend for Chris and I shouldn’t bother him for this. Then spend a few minutes trying to rationalise the low-effort layout by convincing yourself this is not a data-viz blog, and it’s the C# app and the overall approach that is important here. 🙂

The view below shows the result of a real-time trace using a small AdventureWorks model. The FactInternetSales table has ten partitions based on a calendar year. The refresh was triggered using the Power BI refresh, so was a full database refresh.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/05/B11.jpg?resize=1000%2C624&ssl=1)

Creating the Power BI Report in this way is pretty limited. You can’t add measures like this.

Ideally, you now create a new report using Power BI Desktop and use the Get Data button to [live-connect](https://docs.microsoft.com/en-us/power-bi/connect-data/desktop-report-lifecycle-datasets#:~:text=Only%20users%20with%20Build%20permission%20for%20a%20dataset,only%20connect%20to%20one%20dataset%20in%20each%20report.) to a Power BI dataset. You can build out a report that allows you to compare sessions, show event detail, and the example Line and Gantt charts from the previous step.

## Video walk-through

The following video shows

- Creating a streaming dataset in the Power BI web-service
- Creating a Visual Studio Code console app from scratch
- Running the app and watching the visuals change as data streams in
- Building a basic report using the web editor and Power BI Desktop for fundamental analysis

## Summary

Hopefully, this has been helpful and easy to follow. There are a few steps required to get this up and running, but it should be worth it in the end.

The sample code provided works well, but please don’t consider this a finished product. Feel free to tweak and enhance the logic to suit your environment. In this format, all you need is compile the code and make sure it is run on a VM or local machine somewhere.

Some random ideas for how this process could be enhanced:

- More output detail to the console screen, like connection string, current objects running etc.
- Transformations built into the console app – e.g., splitting the ObjectName into Table/Partition
- Run using a service principal to remove interactive requirement and moved to an Azure function?
- Prompts at the start to allow interactive choice of DB/Model to trace
- Configured as an External Tool for Power BI Desktop
- Long-running object alert
- Adapted to use Log Analytics as a source
- Logic ported to PowerShell or another tool that use TOM client libraries

If you prefer to use the full Visual Studio, make sure you use the .net framework TOM client libraries and not the .net Core.

Remember, the database you trace can be anywhere the tool has line-of-sight. The database can be in a different Power BI workspace (or event Tenant), so long as you have permission to run a trace.

Multiple copies of the tool could run on the same machine simultaneously, each tracing a different server. Each copy of the app could send events to the same (or other) endpoints for analysis.

Lastly, this article prompted me to finally set up a new repository in [Github](https://github.com/dax-tips/TracePost) to store code used on this site specifically. Please feel very free to contribute to this. I will jump for joy the first time I see someone commit an update to this or future code.

You can also apply the same approach to [[visualise-your-power-bi-refresh|my previous blog]] which uses SSMS Profiler to capture events. Simply add the ProgressReportCurrent trace event to allow you to add visuals to allow you to study the processing speed for each artifact.

Also, kudos to my colleague Chris Webb for the idea from [this post](https://blog.crossjoin.co.uk/2018/07/19/using-process-monitor-to-find-out-how-much-data-power-query-reads-from-a-file/) of his.

5
7
votes

Article Rating
