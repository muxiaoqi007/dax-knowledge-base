---
title: "Use Fabric Notebook to monitor Power BI semantic models"
url: "https://dax.tips/2023/12/10/use-fabric-notebook-to-monitor-power-bi-semantic-models"
published: "2023-12-10"
updated: "2023-12-10"
source: "dax.tips"
---

![](https://i0.wp.com/dax.tips/wp-content/uploads/2023/12/image-7.png?resize=894%2C467&ssl=1)

If you are starting to enjoy using Microsoft Fabric more, especially the exciting new Direct Lake storage mode, you may find this simple tip useful to help monitor your semantic models.

The idea behind this tip is to run a DMV (can be any DMV) on a regular basis against a semantic-model and accumulate results of each DMV into a table you can analyze later.

The DMV I’m using in this article captures the current state of every column in the model it runs against. In my case, I schedule the snapshot to run every ten minutes and make sure I add a timestamp column to help identify each batch of records.

This method uses the semantic-link python library for Fabric. You will need to include a reference to install the library while testing, however you will need to remove the **%pip install** code block and add the semantic-link python library to your environment when you run the notebook on a schedule.

## Step 1. Create a new Fabric notebook

The notebook does not need to be in the same workspace as the semantic model you plan to monitor. However, you will need the ability to run queries against any semantic model you plan to run the DMV over.

## Step 2. Install semantic-link

This code block is optional and only required if your Spark environment does not have [semantic-link](https://pypi.org/project/semantic-link-sempy/) python library installed by default. The version I’ve used here is 0.4.0.

Read more about configuring your Spark environment [here](https://learn.microsoft.com/en-us/fabric/data-engineering/create-and-use-environment).

You will need to remove this step if you configure the notebook to run on a schedule. The notebook will fail to run and stop when it detects a code block with a **%pip** directive.

```
%pip install semantic-link
```

## Step 3. Run DMV and persist results

The main code block performs the magic. The DMV gets executed at line 4 and the results are assigned to the **df** dataframe. Be sure to update the value at line five to match the name of the semantic model you wish to snapshot. Make sure you also update line 20 with the name of the workspace the semantic model resides.

Once the DMV has run, a timestamp column is added to help group all the rows from the DMV together. This is managed by lines 23 and 24.

Finally, the **df.to\_lakehouse()** method appends the contents of DMV (including the timestamp column). The **to\_lakehouse()** method creates the Lakehouse table automatically for you the first time this code is run.

```dax
import sempy.fabric as fabric
import datetime

df = fabric.evaluate_dax(
    ds="MY_MODEL_NAME_HERE",
    
    """SELECT 
		MEASURE_GROUP_NAME,
		ATTRIBUTE_NAME ,
		DATATYPE ,
		DICTIONARY_SIZE ,
		DICTIONARY_ISPAGEABLE ,
		DICTIONARY_ISRESIDENT ,
		DICTIONARY_TEMPERATURE ,
		DICTIONARY_LAST_ACCESSED
	
			FROM $SYSTEM.DISCOVER_STORAGE_TABLE_COLUMNS 
			ORDER BY [DICTIONARY_TEMPERATURE] DESC
	""",
	"MY_WORKSPACE_NAME_HERE")

# Append timestamp column
now = datetime.datetime.now()
df["timestamp"] = now
df.to_lakehouse_table("dmv_log","append")

```

## Step 4. Run to test

All going well, these two code-blocks should execute without error and generate a new delta table called **dmv\_log** in the Lakehouse the notebook is connected to (doesn’t need to be in the same workspace as the notebook).

For fun you can now jump over to your SQL endpoint and start running queries over the table to see data accumulate. You can also use Power BI or python to study the data.

## Step 5. Configure Schedule

The fun really begins when you can run this notebook on a schedule.

From the Run menu option on the ribbon, click the Schedule button.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2023/12/image-5.png?resize=1000%2C781&ssl=1)

Here I have a schedule that runs every 10 minutes. You can use the All Runs button (next to the Schedule button) to explore the results of each run once the schedule is underway.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2023/12/image-6.png?resize=1000%2C949&ssl=1)

## That’s is!

You can sit back, relax and enjoy seeing data accumulate in the **dmv\_log** table. I set this up and had to get on the road so could only monitor the results from my phone. I was surprised and impressed how well Fabric works from a mobile device. I was able to type out a few SQL Endpoint queries on my phone to review the new data coming in every 10 minutes.

## Summary

Once again, the sample code provided is intended to be a starting point for you to tweak and enhance to suit. You may want to create a python script around the **fabric.list\_datasets()** function and iterate can capture several models in each run.

When you have data coming in, you can analyze the data (using Power BI of course!) to build a picture of when columns get paged in/out of memory. Or you can SUM the size column to see how much memory be being used by data in the model before or after reframing etc.

Other DMV’s can be used in place of the once used in the example. Just substitute your own code in place of lines 7 through 18 in the main code block. Or you can explore and play with the new INFO functions that looks like they have lots of interesting goodies. No doubt my good friend Chris Webb will blog about most of those. 🙂

I have used this same technique outside Fabric using VS Code and PowerShell, however, this is much easier to setup.

I hope you find this helpful and as always, let me know what you think.

4.8
4
votes

Article Rating
