---
title: "Visualise your Power BI Refresh"
url: "https://dax.tips/2021/02/15/visualise-your-power-bi-refresh"
published: "2021-02-15"
updated: "2021-02-15"
source: "dax.tips"
---

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/02/B1-1.jpg?resize=1000%2C570&ssl=1)

Have you ever wondered why a Power BI dataset refresh was taking so long? And more specifically, how much time did the refresh spend on various sub-tasks that aren’t that visible to you via the web-portal?

This article shares a technique you can use to capture events fired during a Power BI refresh and use the results in a Power BI report visualise the results. Have to love the idea of using Power BI to optimise and improve Power BI. 🙂

Imported Power BI models get hosted in an Analysis Services database which emits telemetry for all sorts of interesting activities that take place. There are events related to end-user queries, security events, and of course, events related to dataset refreshes.

Once you have telemetry events, you can easily visualise the results in Power BI to help you understand how you might optimise future refreshes.

The high-level plan for this is:

1. **Connect to Analysis Services and run a trace using [SSMS Profiler](https://aka.ms/ssmsfullsetup)**
2. **Kick off a dataset refresh**
3. **Save the trace file as a Trace XML file**
4. **Import the trace file into a [PBIX](https://1drv.ms/u/s!AtDlC2rep7a-9w2ZojWAtLS-L15t?e=SnLLvV) from step 3 to see the results** ([download the PBIX here](https://1drv.ms/u/s!AtDlC2rep7a-9w2ZojWAtLS-L15t?e=SnLLvV))

This technique works against a local Power BI Desktop file, against datasets hosted in a Power BI Premium workspace, AAS and local SSAS instances. The method also works with datasets hosted in a Premium Per User workspace, but will not work against a dataset hosted in a Power BI Pro workspace.

Below is a video walk-through of the exercise.

## Step 1: Run a Trace

If you don’t already have SSMS (SQL Server Management Studio) installed on your machine, download and install it here: <https://aka.ms/ssmsfullsetup>

SSMS is a free tool built and maintained by Microsoft to help manage traditional SQL Server and Analysis Services database engines. As part of SSMS, you get a tool called SQL Server Profiler. This tool can be configured to run *traces* against a database engine to capture and extract interesting telemetry. Traces are manually started/stopped by you and can configure to filter/capture only specific events.

Once SSMS is installed, you start SQL Server Profiler via SSMS and select SQL Server Profiler from the Tools menu (shown below).

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/02/B3.jpg?resize=529%2C455&ssl=1)

Or, you can start SQL Server Profiler from your Windows menu system. I start typing “Profiler” from the Windows 10 start menu until the popup menu shows the app.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/02/B4-1.jpg?resize=408%2C713&ssl=1)

Once SQL Server Profiler starts, start a new trace from the File menu.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/02/B5-1.jpg?resize=303%2C240&ssl=1)

The next step is to connect to the instance of Analysis Services engine you would like to trace. In the **Connect to Server** dialog, make sure the Server Type gets set to Analysis Services, rather than the default of Database Engine.

The Server name option is the location of your AS dataset. For connecting to Power BI Desktop, this will be something like “localhost:1234” – where you replace 1234 with the current port number used by Power BI Desktop (this changes each time you close/open Power BI).

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/02/B6-1.jpg?resize=492%2C330&ssl=1)

Once you have SSMS on your machine, you can follow [these helpful steps](https://www.biinsight.com/quick-tips-registering-sql-server-profiler-as-an-external-tool/) described by Soheil Bakhshi to register SQL Server Profiler as an external tool in the Power BI Ribbon.

Suppose you want to connect to a dataset in a Power BI Premium workspace. In that case, you will need to make sure the Server name option matches the **Workspace Connection**property from the workspace settings in the Power BI web-service.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/02/B9-1.jpg?resize=494%2C475&ssl=1)

Before you click the connect button, you need to specify which dataset you would like to trace in the workspace. To do this, click the Options button from the Connect to Server dialog.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/02/B10.jpg?resize=492%2C330&ssl=1)

In the **Connection Properties** tab, select the <Browse server..> item from the Connect to database drop-down. This action will probably initiate authentication popups, and when successful, you should see a dialog listing all the datasets in the specified workspace. Click the **Connect** button at the bottom of this dialog when you are ready.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/02/B8-2.jpg?resize=492%2C535&ssl=1)

The next screen is where we define which events we want to capture. For this blog, we are only interested in dataset refresh related events. First, make sure the **Use the template** option is set to Blank then click the Events Selection tab at the top.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/02/B7.jpg?w=1000&ssl=1)

In the Events Selection tab, expand and click the **Job Graph** event along with the **Progress Report End** event. If the Job Graph event doesn’t appear, make sure you are using the latest version of Power BI Desktop (if you are connecting to Power BI Desktop) and make sure you are on the latest version of SSMS.

Clicking the left-most checkbox on each row automatically checks all the checkboxes in that row.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/02/B2.jpg?w=1000&ssl=1)

Finally, click the Run button in the lower right-hand corner to start the trace. If you are connecting to Power BI Premium, this currently can take 20 to 30 seconds to start working.

## Kick off a dataset refresh

If you are tracing a Power BI Premium dataset, you can refresh your database the usual way through the web-portal. You can also run a refresh using SSMS (will describe in my next blog) so that you can flexibly pick and choose which tables/partitions get included in the refresh.

Otherwise, if you connect to a Power BI Desktop dataset, just hit the refresh button in Power BI Desktop to initiate the refresh.

If successful, **SQL Server Profiler** will start to show lines of activity after a short pause. This activity is what we want to import into the Power BI Report we will use to analyse/visualise the refresh.

## Save the Trace File

Once you know the refresh is complete, you can pause/stop the trace file and save the output into a local file.

Hopefully, you see a series of Job Graph events in the **EventClass** category/column.

Be sure to save the trace as a **Trace XML file**, rather than a Trace File. The Power BI Report used to analyse this data is configured to work with the XML version of the trace data. Please note the location you save the file as this will be needed to tell our Power BI File what to import.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/02/B11.jpg?resize=519%2C404&ssl=1)

## Analyse the Trace File

Once you have the trace file, open the [Analyse my Refresh.pbix](https://1drv.ms/u/s!AtDlC2rep7a-9w2ZojWAtLS-L15t?e=SnLLvV) file and import the data. You will need to open the Power Query Editor and set the location of your saved Trace XML file into the parameter highlighted. Note, you need to include the full path and filename extension into the parameter for this to work.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/02/B12.jpg?resize=1000%2C478&ssl=1)

Once you update this parameter, click Close & Apply to read in the results of your trace and start studying your refresh.

## Understanding the results

The **Analyse my Refresh** PBIX file does not prescribe direct answers to any refresh challenges you have. Instead, it is useful in helping you understand how long each item takes to refresh. Hopefully, the report highlights tables/partitions which are fast to complete, along with those that take the most time.

The chart on

![](https://i0.wp.com/dax.tips/wp-content/uploads/2021/02/B13-1.jpg?resize=1000%2C573&ssl=1)

Remember, if a table/partition takes a long time to refresh, this could reflect it has a large number of rows/columns and not necessarily a problem.

The top chart shows each table/object on the Y-axis. The bars on the Gantt chart represent how much time was spent on each step for that object. The blue Execute SQL step shows when the query was issued until the first row gets returned from the data source. The yellow bar shows the time spend by Analysis Services receiving the stream of data and performing tasks such as encoding/compressing into columns.

A long blue bar could highlight some opportunities to performance tune the data source, such as increasing compute or a better index strategy. This timing could also highlight Power BI’s query calling a series of Views on Views on Views. You’d be surprised to see how often I come across this. If you suspect this to be an issue in your case, try temporarily creating physical tables in the data source and see what effect this has on your refresh.

A long yellow bar could mean the table has many rows/columns and I have some tips on how you can address this below. This could also mean the data source is struggling to deliver data. If you are using a gateway, perhaps the gateway machine is under CPU/Memory pressure. Or, you are potentially doing a lot of complex Power Query transformations that might be slowing down the system.

Consider the number of rows read per object by how long it takes and if you still want to improve refresh speed per object.

- Move the data to a different (faster) data source. e.g. use a [Dataflow](https://ssbipolar.com/category/power-bi/dataflows/), or SQL DB instead of an Excel file.
- Optimise the number of transformations taking place in Power Query
- Review your Gateway (if relevant)
- Reduce the number of rows read per table
- Reduce the number of columns per table
- Convert tables with large numbers of rows into partitions
  - Must be configured in Power BI Premium using XMLA-RW aware tools such as SSMS, Tabular Editor, SSDT etc. These can get automated with TOM.
  - Cannot be configured using Power BI Desktop
  - Partitions are excellent for large fact tables and allow fine-grain partition loading and DIY incremental refresh
  - Partitions can refresh in parallel – which is a great way to reduce overall time, but ensure your data source can keep up
- If you double the number of columns in a table, you often double the time each table takes to refresh

The matrix beneath the Gantt chart shows the number or rows processed for each table/partition along with a useful ratio that derives the number of rows per second. The ratio column can be useful to sort by to help figure out which objects under/over perform.

In the example from the image, an Excel table took 37 seconds to process only four rows.

The second report page called **Using Slot Data**, is exactly the same data as the **Refresh Times** page. However, in the **Using Slot Data** report, the Y-axis on Gantt chart is organised into Slots. This is especially interesting for datasets with lots of objects hosted in Azure AS and Power BI Premium. Both systems will limit the number of concurrent slots (or containers) available for the refresh. Once a table/partition has finished processing, the next unprocessed object will begin. In Azure AS you can control the number of slots using the maxParallelism property. Increasing this property does not always lead to faster overall refresh times.

## Summary and Notes

If you run a trace against a model hosted by Power BI Desktop, you will notice the AS engine refreshes all table objects/partitions at once. Datasets hosted in Power BI Premium will stagger the objects depending on several factors, but you may only see a handful running simultaneously. Several factors determine the number of slots/containers available to a Power BI Refresh. Some of which are under your control.

In essence, this tool is not designed to solve your Power BI Refresh challenges – but hopefully, it helps you get to an “Aha!” moment when you discover the one or two items causing your refresh to take as long as it does.

Some other easy things to try on a slow dataset are creating a clone without the slowest table(s) and then checking to see how fast a new refresh can complete.

Hopefully, this is helpful. Please feel free to let me know how you get on. I’m always interested to hear if these articles are of use.

Feel free to also tweak and improve the report as you need. Let me know if you come up with improvements worthy of sharing with the wider community.

## Thanks

A big thanks to Chris Hamill over at [Alluring Analytics | A Power BI Creator Blog (alluringbi.com)](https://alluringbi.com/) for the excellent design for the PBIX file.

[Alex Barbeau](https://twitter.com/AlexOnData) for helping test drive a version of this with some great improvements.

Adam and Patrick at GuyInACube for inviting me on their [live stream](https://www.youtube.com/watch?v=e_yuomKr6p4) to talk a little about this over the weekend.

5
27
votes

Article Rating
