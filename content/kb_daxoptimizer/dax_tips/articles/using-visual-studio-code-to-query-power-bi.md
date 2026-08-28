---
title: "Using Visual Studio Code to query Power BI"
url: "https://dax.tips/2020/08/24/using-visual-studio-code-to-query-power-bi"
published: "2020-08-23"
updated: "2020-08-24"
source: "dax.tips"
---

In this article, I’m going to show how you can use [Visual Studio Code](https://code.visualstudio.com/) to run queries against a Power BI model. Usually, this is the kind of task you would use the excellent [DAX Studio](https://daxstudio.org/) for, but sometimes you have a requirement that doesn’t quite fit DAX Studio. Using VSCode provides a quick and flexible alternative that you can customise to suit, e.g. create customised extracts

One scenario that I did get asked about recently is how to create measures in a model dynamically based on data. This particular scenario is covered by example 2 in this article.

## Client Libraries

It’s helpful to understand there are two main client libraries for Analysis Services. A client library is what you can add to any new Visual Studio Code Project to provide objects, methods and functions relevant for the tool you are building.

Make sure you download the NetCore (.Net Core) versions of these libraries when working with Visual Studio Code. There are .Net Framework versions of these libraries that are more suited to use with the full [Visual Studio](https://visualstudio.microsoft.com/) product.

The two Analysis Services client libraries are:

- [AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.NetCore.retail.amd64)
- [AMOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.NetCore.retail.amd64)

### AMO

The **AMO** client library provides your VSCode project with functions and objects that allow you to connect to an Analysis Services model and manage the physical structure of the model. Tasks such as add/change/removing tables, columns, measures, relationships etc. My two previous articles used this client library for this and often gets referred to as using TOM or the Tabular Object Model. If you are a regular user of Tabular Editor, then consider this client library as the one you would use to automate the kinds of tasks you might perform using [Tabular Editor](https://tabulareditor.com/).

### AMOMD

The AMOMD client library allows you to connect to an Analysis Services model and issue DAX queries and receive results. Think of AMOMD as enabling you to perform tasks you might otherwise use [DAX Studio](https://daxstudio.org/) for. Exercise 1 below introduces this client library. Of course, your project can include tasks using both libraries for some really creative fun.

## Exercise 1: Use Visual Studio Code to Query Power BI Desktop

The first exercise is going to show how to run queries from Visual Studio Code against Power BI.

In this exercise, we will set up a new project in Visual Studio Code, add the AMOMD client library and then write some basic query code.

Please make sure you have already completed the first two steps from my previous [[using-visual-studio-code-with-power-bi|article here]] to :

1. Downloaded and installed Visual Studio Code
2. Installed the latest .Net Core SDK

### Step 1: Create Visual Studio Project

Create a blank folder somewhere on your machine. I created a new folder on my machine called **c:\vscode\QueryTool**

Open Visual Studio Code and from the File menu, select Open Folder and navigate to, and open the blank folder created previously.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/08/B1.png?resize=755%2C435&ssl=1)

### Step 2: Create .Net Console Project

Once you have opened your folder in Visual Studio Code, issue a command in a PowerShell Terminal window to create the .Net project.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/08/B2.png?resize=1000%2C334&ssl=1)

Once the terminal window appears, issue the following command

```
dotnet new console
```

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/08/B3.png?resize=1000%2C323&ssl=1)

### Step 3: Install AMOMD client Libraries

The next step is to add the AMOMD client Libraries. You add the libraries using the same Terminal window from the previous step. To find the most recent version of the library, open the following URL in your browser to load details of available versions.

<https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.NetCore.retail.amd64>

At the time of writing this blog, the latest version is 19.9.0.1-Preview. In the Nuget page, click the **.Net CLI** tab to show the client library interface command required and then copy this text into your clipboard.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/08/B4.png?resize=906%2C1024&ssl=1)

Finally, paste the text into the terminal window back in your Visual Studio Code project.

```
dotnet add package Microsoft.AnalysisServices.AdomdClient.NetCore.retail.amd64 --version 19.9.0.1-Preview
```

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/08/B5.png?resize=1000%2C108&ssl=1)

This is all that is needed to get us ready to write code!

### Step 4: Start writing code! 😀

Open the **Program.cs** file from the left-hand explorer by double-clicking the file name at (1). This action opens the file and shows 12 lines of text in the area indicated by (2).

If you see a dialog box in the bottom right-hand corner like (3), just click the Yes button (4).

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/08/B7.png?resize=1000%2C924&ssl=1)

The Power BI model I will connect to in this example can be downloaded here: [AdventureWorksDemoPBIX](https://github.com/MicrosoftDocs/mslearn-dax-power-bi/raw/main/activities/Adventure%20Works%20DW%202020%20M01.pbix)

Once you have **Program.cs** open, paste the following code over the top of all the existing code.

Please note, you will need to update the port number in the connection string in line 14 of this example to match your instance of Power BI Desktop. [How to find the port number?](https://www.biinsight.com/four-different-ways-to-find-your-power-bi-desktop-local-port-number/)

```dax
using System;
using Microsoft.AnalysisServices.AdomdClient;

namespace QueryTool
{
    class Program
    {
        static void Main(string[] args)
        {
            /*******************************************************
                Define Connection
            *******************************************************/

            AdomdConnection adomdConnection = new AdomdConnection("Data Source=localhost:50834");
           
            /*******************************************************
                Define Query (as a Command)
                - the AdomdCommant uses the above connection
                - subsitute this for your own query
            *******************************************************/

            String query = @"
            EVALUATE
                SUMMARIZECOLUMNS(
                    //GROUP BY 
                    Customer[Country-Region] ,
                    
                    //FILTER BY
                    TREATAS( {""Red"",""Blue""} , 'Product'[Color] ) ,
                    
                    // MEASURES
                    ""Sum of Sales Amount"" , SUM(Sales[Sales Amount]) ,
                    ""Sum of Order Quantity"" , SUM(Sales[Order Quantity])
		        )
            ";
            AdomdCommand adomdCommand = new AdomdCommand(query,adomdConnection);

            /*******************************************************
                Run the Query
                - Open the connection
                - Issue the query
                - Iterate through each row of the reader
                - Iterate through each column of the current row
                - Close the connection
            *******************************************************/

            adomdConnection.Open();
            
            AdomdDataReader reader = adomdCommand.ExecuteReader();

            // Create a loop for every row in the resultset
            while(reader.Read())
            {
                String rowResults = "";
                // Create a loop for every column in the current row
                for (
                    int columnNumber = 0;
                    columnNumber<reader.FieldCount;
                    columnNumber++
                    )
                {
                rowResults += $"\t{reader.GetValue(columnNumber)}";
                }
                Console.WriteLine(rowResults);
            }
            adomdConnection.Close();
        }
    }
}

```

Notes about the code :

- You define your query at line 22. The @ command at the start of the string allows you to space your query over multiple lines. The ” character needs to be converted to “” as shown in lines 29, 32 and 33.
- A query file could be read in from a file instead of by using the following at line 22:  
  String query = System.IO.File.ReadAllText(@”myQuery.dax”);

Video summary of exercise

Challenges for you :

- Change the code to read a DAX query from a file.
- Add the Column headers to the output.
- Write the results out to a file.

## Exercise 2: Use query results to create measures!

The second exercise in this blog will build on the existing project to combine the results of a query (using AMOMD client library), with TOM to add measures to the model.

The object is to dynamically create a series of measures that perform a SUM over a particular column. In this case, it will be based on the Product Colour but could easily use other columns in the model.

### Step 1: Add the AMO client libraries (TOM)

Using the existing project, add the new package. To determine the correct path, open the following URL in a browser to get the latest **.Net CLI** to install the package:

<https://www.nuget.org/packages/Microsoft.AnalysisServices.NetCore.retail.amd64>

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/08/B8.png?resize=945%2C1024&ssl=1)

In my case, the latest copy to install is

```
dotnet add package Microsoft.AnalysisServices.NetCore.retail.amd64 --version 19.9.0.1-Preview
```

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/08/B9.png?resize=1000%2C139&ssl=1)

### Step 2: Write DAX to get a list of values

Over in DAX Studio, I created a simple query to generate a list of all the values in the ‘Product'[Color] column. I save this query as a file called **QueryColurs.dax** in the same folder as my Visual Studio Code project (c:\vscode\QueryTool).

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/08/B10.png?resize=1000%2C784&ssl=1)

### Step 3: Write some C# code to create measures

The last step is to overwrite all the existing code in the **Program.CS** with the following snippet and again, take care to update the current port number at line 12.

```
using System;
using Microsoft.AnalysisServices.AdomdClient;
using Microsoft.AnalysisServices.Tabular;

namespace QueryTool
{
    class Program
    {
        static void Main(string[] args)
        {

            String currentPort = "50834";

            /*******************************************************
                Define Connection
            *******************************************************/

            AdomdConnection adomdConnection = new AdomdConnection($"Data Source=localhost:{currentPort}");

            /*******************************************************
                Connect to Power BI Desktop using TOM
            *******************************************************/

            Server server = new Server();
            server.Connect($"localhost:{currentPort}");
            Model model = server.Databases[0].Model;
            Table salesTable = model.Tables["Sales"];
           
            /*******************************************************
                Define Query (as a Command)
                - the AdomdCommant uses the above connection
                - subsitute this for your own query
            *******************************************************/

            String query = System.IO.File.ReadAllText("QueryColours.dax");
            AdomdCommand adomdCommand = new AdomdCommand(query,adomdConnection);

            /*******************************************************
                Run the Query
                - Open the connection
                - Issue the query
                - Iterate through each row of the reader
                - Iterate through each column of the current row
                - Close the connection
            *******************************************************/

            adomdConnection.Open();

            AdomdDataReader reader = adomdCommand.ExecuteReader();

            String measureDescription = "Auto Measure";



            /*******************************************************
                Clear any previously created "Auto" measures
            *******************************************************/
            foreach(Microsoft.AnalysisServices.Tabular.Measure m in  salesTable.Measures)
            {
                if(m.Description==measureDescription)
                {
                salesTable.Measures.Remove(m);
                model.SaveChanges();
                }

            }

            /*******************************************************
                Create the new measures
            *******************************************************/            

            // Create a loop for every row in the AMOMD resultset
            while(reader.Read())
            {
                String myColour = reader.GetValue(0).ToString();
                String measureName = $"Sum of {myColour} Sales Amount";

                Microsoft.AnalysisServices.Tabular.Measure measure = new Microsoft.AnalysisServices.Tabular.Measure()
                    {
                        Name = measureName,
                        Description = measureDescription ,
                        DisplayFolder = "Auto Measures" ,
                        FormatString = "Currency" ,
                        Expression = $@"
                            CALCULATE (
                                SUM('Sales'[Sales Amount]) ,
                                'Product'[Color] = ""{myColour}""
                            )
                        ",
                    };
                salesTable.Measures.Add(measure);
            }

            model.SaveChanges();
            adomdConnection.Close();
        }
    }
}
```

Notes about the code:

- Lines 20 to 27 create a TOM connection to the model.
- Line 35 reads a query in **QueryColours.dax** file that needs to be in the same folder as the code.
- Line 58 uses the fully qualified namespace for Measure to ensure we are using the AMO version of a Measure rather than the AMOMD version.
- Lines 58 through 66 clear out any measures previously created using this technique but leaving other measures in the model alone.

Once run in Visual Studio Code, you should see the following in your Power BI Desktop instance.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/08/B11.png?resize=1000%2C798&ssl=1)

And that’s it!

A video version of this example is available here:

This exercise is an example of how you can combine the two Analysis Services client libraries (AMO and AMOMD) inside Visual Studio Code to help perform tasks.

## Summary

The Analysis Services client libraries provide fantastic flexibility to create a library of utility scripts that can complete pretty much any task.

I’m not recommending this approach as a replacement to the excellent Tabular Editor, or DAX Studio as these are my go-to tools of choice. However, for edge cases, it’s nice to know that you can easily use Visual Studio to work with your model.

I’ll have had a crack at recording these as videos. Please do not expect the excellent quality of my team-members ([GuyInaCube](https://guyinacube.com/) and [BIPolar](https://www.youtube.com/channel/UCpsilPn-2qFlrYYuvyFkpPQ)), but let me know in the comments if this format helped you out.  
  
[Video of Exercise 1](https://youtu.be/T7T8D0-2ytM)  
[Video of Exercise 2](https://youtu.be/cDFpoUYY5Y0)

4.6
5
votes

Article Rating
