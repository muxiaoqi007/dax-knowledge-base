---
title: "Using Visual Studio Code with Power BI"
url: "https://dax.tips/2020/07/09/using-visual-studio-code-with-power-bi"
published: "2020-07-09"
updated: "2020-07-09"
source: "dax.tips"
---

For this blog, I want to share a technique that allows you to use tools such as [Visual Studio Code](https://code.visualstudio.com/) to connect and make changes to Power BI models. The technique not only allows you to browse the underlying model easily but also enhance and change such as adding/changing measures.

Power BI data models are hosted in an instance of an Analysis Services, either running locally in Power BI Desktop or published to the web service.

Analysis Services provides the ability for external tools to connect and manage models using the [Tabular Object Model (TOM)](https://docs.microsoft.com/en-us/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo). We already have fantastic tools such as [SSDT](https://docs.microsoft.com/analysis-services/tools-and-applications-used-in-analysis-services), [Tabular Editor](https://tabulareditor.github.io/) and [ALM toolkit](http://alm-toolkit.com) that use TOM to connect and update existing data models. Still, *sometimes* you have a task that isn’t so easy to complete one of the existing tools.

Many of these tasks can be completed by creating TOM based scripts to run in your tool of choice. We could create scripts to run in Visual Studio or PowerShell for quite a while, but a recent update to the TOM client libraries now make it very easy to use Visual Studio Code for such requirements.

Previously, TOM client libraries only supported the .Net framework, which meant they were complicated to use with Visual Studio Code. Now the TOM client libraries also have a .Net Core version that works nicely with Visual Studio Code.

### Why Visual Studio Code?

Visual Studio Code is a reasonably new development environment which is lightweight and quick to install and get up and running. There is nothing you can do in VS Code that you can’t also do in another tool using TOM. I just thought it would be fun to show how quick and easy it is to get up and running in VS Code in very few steps.

The following exercise uses VS Code to connect and manage a Power BI Desktop model. You can also connect to models hosted in Azure Analysis Services as well as models hosted in Power BI Premium.

### The exercise

The following step by step exercise will be to download and configure VS Code to the point where it can connect to a Power BI Desktop file and add a new measure. The exercise will be on a clean VM.

#### Step 1 : Download Visual Studio Code

Open a browser and navigate to [code.visualstudio.com](https://code.visualstudio.com/) then download and run the installer for the current version for Windows. Use default settings when prompted during the install.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/07/B1.png?resize=808%2C628&ssl=1)

#### Step 2 : Install .Net Core SDK

On a browser navigate to the [download page](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.301-windows-x64-installer) for the .Net Core SDK. Note this is not the same as the .Net Core runtime (which gets installed by the SDK) – it needs to be the SDK. I use version 3.1.301, so any version that matches this or later is fine.

Download and install the SDK executable using default settings.

Once the SDK has installed, you can check by opening VS Code and running the following command at a terminal:

```
dotnet --info
```

To open a new terminal window in VS Code, click the Terminal menu item from the top – then select **New Terminal** from the drop-down menu. This action will open an area at the bottom of the VS Code screen where you can type commands such as **dotnet –info**

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/07/B2.png?resize=1000%2C752&ssl=1)

The **dotnet –info** command should display a list of text in the terminal window, and we are looking to see that the .Net Core SDK install was successful as per the following image (you may need to scroll up):

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/07/B3.png?resize=1000%2C754&ssl=1)

#### Step 3 : Create a C# console app

At the terminal window, type the following three commands to create a brand new folder for our project.

```
md PBI-Tool

cd PBI-Tool

dotnet new console
```

The first command creates a new empty folder (md = make directory) for the exercise. The second command moves into the new folder (cd = change directory), while the final command runs some scripts to download a template for a c# console app in the new folder.

Browsing this new folder from File Explorer should show the following :

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/07/B4.png?resize=623%2C299&ssl=1)

#### Step 4 : Add TOM client libraries

Now we have a sample application; we can add the [.Net Core TOM client libraries](https://www.nuget.org/packages/Microsoft.AnalysisServices.NetCore.retail.amd64) by typing the following command into a terminal window.

```
dotnet add package Microsoft.AnalysisServices.NetCore.retail.amd64 --version 19.4.0.2-Preview
```

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/07/B5.png?resize=1000%2C758&ssl=1)

This command downloads and installs the relevant DLL files needed to connect with Analysis Services databases.

#### Step 5 : Open the project

VS Code works with folders, so to open the newly created .Net project, we need to click the Open Folder button in the top right, and then navigate to the PBI-Tools folder created using the md command at step 3.

If you are unsure where the folder is, you can derive this from the prompt in the terminal window. In my case, the folder is **c:\Users\seamark\PBI-Tool**.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/07/B7.png?resize=1000%2C751&ssl=1)

Once VS Code has opened the folder, you can open the Program.cs file shown at step 1. If you see a prompt to install a C# extension in the lower right-hand corner, click install, including any additional dialogs asking

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/07/B6.png?resize=1000%2C756&ssl=1)

And that’s it. We are all set up and ready to start writing code!

#### Step 6 : Write some TOM code

First off, we will create a small script that will connect to a local instance of Power BI Desktop and display a list of all the table names.

Paste the following code into the VS Code editor over the top of all the existing code. The port number shown in line 11 will need to be updated.

You can use any of [these techniques](https://www.biinsight.com/four-different-ways-to-find-your-power-bi-desktop-local-port-number/) to find the current port number of your Power BI Desktop file. Note this will change around and will not match the number shown in my example.

```
using System;
using Microsoft.AnalysisServices.Tabular;

namespace PBI_Tool
{
    class Program
    {
        static void Main(string[] args)
        {
            Server server = new Server();
            server.Connect("localhost:50654");

            Model model =  server.Databases[0].Model;

            foreach(Table table in model.Tables)
            {
                Console.WriteLine($"Table : {table.Name}");
            }
        }
    }
}
```

When you are ready to run, click the **Start Debugging** menu option from the Run menu, or press F5. If you get prompted for a debugging environment, chose .Net Core.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/07/B9.png?resize=1000%2C750&ssl=1)

The code should connect to Power BI Desktop and iterate through every table in the model and write the name of each table out to the console window.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/07/B10.png?resize=530%2C275&ssl=1)

#### Step 7 : Bonus Code

As a bonus step, here is an example of how you can add a measure to the model we just connected in the previous step. This script connects to the same model as previously done, and then looks for a table called “Sales” at line 14 (you may need to change this to match the name of a table in your model).

Then the code checks to see if there is already a measure called “VS Code Measure”. If it can’t find one, it creates a new measure and adds it to Power BI Desktop. If you re-run the script a second time, it will update the contents of the measure to a different DAX expression.

```
using System;
using Microsoft.AnalysisServices.Tabular;

namespace PBI_Tool
{
    class Program
    {
        static void Main(string[] args)
        {
            Server server = new Server();
            server.Connect("localhost:50654");

            Model model =  server.Databases[0].Model;
            Table table = model.Tables["Sales"];

            if (table.Measures.ContainsName("VS Code Measure"))
            {
                Measure measure = table.Measures["VS Code Measure"];
                measure.Expression = "\"Hello Again World\"";
            }
            else
            {
                Measure measure = new Measure()
                {
                    Name = "VS Code Measure",
                    Expression = "\"Hello World\""
                };
                table.Measures.Add(measure);
            }
            model.SaveChanges();
        }
    }
}
```

Press F5 to run and voila! Jumping over to Power BI Desktop should show a new measure in the Sales table which shows the text “Hello World” when added to the canvas. Run the report a second time will update the expression to now show “Hello Again World”.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/07/B11.png?resize=740%2C578&ssl=1)

### Summary

You can do much more with TOM than simply print a list of tables or update measures. This article is just the first of a series of posts I plan to write describing some of the more useful things you might like to try, such as:

- Bulk copying measures from one Power BI report to another
- Auto creating “Sum of”, “Max of” measures for every numeric column in your model
- Migrating AAS models to Power BI Premium
- Customizing Power BI Desktop by registering your C# console app as an external tool to the toolbar

I’m not suggesting or recommending this approach should be your primary method of working with Power BI models. We have wonderful tools already – but it is always good to know you can quickly connect to your models to complete some tasks that aren’t so easy in existing tools.

Not all features of TOM work with Power BI desktop. You can browse the entire model, but only add particular objects such as measures. Currently, you cannot add a table to Power BI Desktop – although this will work if you connect to a model hosted in Power BI Premium.

Working with the TOM like this is a great way to improve your understanding of the AS database engine and will also improve your experience when using some of the advanced features of Tabular Editor.

### Quickfire Summary of steps

1. Install VS Code
   1. <https://code.visualstudio.com>
2. Install .Net Core SDK
   1. <https://dotnet.microsoft.com/download/dotnet-core>
3. Create new console app in new folder
   1. dotnet new console
4. Add TOM client libraries
   1. dotnet add package Microsoft.AnalysisServices.NetCore.retail.amd64 –version 19.4.0.2-Preview
5. Write your code
   1. using Microsoft.AnalysisServices.Tabular;
   2. Server server = new Server();
   3. server.connect(<your AS DB here>) ;

5
31
votes

Article Rating
