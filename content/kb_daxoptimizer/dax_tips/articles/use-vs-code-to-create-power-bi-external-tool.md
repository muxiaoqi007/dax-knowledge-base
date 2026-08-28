---
title: "Use Visual Studio Code to create Power BI External Tool"
url: "https://dax.tips/2020/10/19/use-vs-code-to-create-power-bi-external-tool"
published: "2020-10-19"
updated: "2020-10-20"
source: "dax.tips"
---

For this article, I want to share a way for you to create your own Power BI “Helper Tool” and register it as an external tool in Power BI. This article carries on from some of my recent articles on how you can use Visual Studio Code to help automate specific tasks by taking advantage of the existing Analysis Services client libraries.

In my role, I often connect to AS models (Power BI or Azure AS) and often want to perform specific tasks quickly. The helper tool I share here allows you to connect easily to an AS model and then perform helpful tasks. I’ve deliberately kept the look and feel of the tool to be ‘old school’ like me. 🙂

This particular tool only has a few options available, but you should easily be able to extend this to suit your workload requirements if need.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/10/ani1-1.gif?resize=1000%2C704&ssl=1)

## The Steps

1. Create a new empty folder
2. Open folder in VS Code
3. Create a dotnet project
4. Install Client Libraries
5. Copy code
6. Edit launch.json
7. Add to Power BI as External Tool

A video walkthrough of this exercise can be [found here](https://youtu.be/AacRk2DRaWA).

### Step 1: Create a new empty folder

On the machine you have [Visual Studio Code](https://code.visualstudio.com/) installed, create a new empty folder on the machine. For my example, I made a new folder called **c:\vscode\ET1**

### Step 2: Open folder in VS Code

Open the above folder in VS Code. Make sure you have already installed the .Net Code SDK, which is step 2 from a [[using-visual-studio-code-with-power-bi|previous article.]]

### Step 3: Create a dotnet project

From the terminal window, type the following command, which creates the project in the current folder.

```
dotnet new console
```

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/10/B1.jpg?resize=1000%2C639&ssl=1)

### Step 4: Install Analysis Services Client Libraries

Install two A.S. client libraries to our project. To install the client library to our package, click on the links below to open the Nuget website on the correct page.

[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.NetCore.retail.amd64)

[AMOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.NetCore.retail.amd64)

Then copy the text from the .Net CLI tab for each and run it in the same Terminal window from the previous step (a new Terminal window is also ok).

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/10/B2.jpg?resize=1000%2C561&ssl=1)

Make sure you do this for both the AMO and the AMOMD libraries. The code at the time of writing this article for me is:

```
dotnet add package Microsoft.AnalysisServices.NetCore.retail.amd64 --version 19.12.3-Preview
```

```
dotnet add package Microsoft.AnalysisServices.AdomdClient.NetCore.retail.amd64 --version 19.12.3-Preview
```

### Step 5: Copy Code

Copy ALL the following code from over the top of ALL text in the Program.cs file from the following share:

[VS Code Asset Folder](https://1drv.ms/u/s!AtDlC2rep7a-9WJOGr09KbK7Q3Zb?e=uoEMFH)

### Step 6: Edit Launch.json

Edit the Launch.json file from the .vscode folder to update the console property from **internalConsole** to **externalTerminal**. Make sure you save the file once you update this property.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/10/B4.jpg?resize=1000%2C494&ssl=1)

At this point, you can run the application in VS Code, and it should detect and list any instances of Analysis Services it finds on the local machine (Power BI, or SSAS). If you have a copy of Power BI Desktop open, you’ll see it listed as localhost:nnnnn where the nnnnn is a number approximately around 50,000.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/10/B5.jpg?resize=693%2C268&ssl=1)

You can either type a number to connect to a detected instance of AS, or type in the servername of an Azure-AS, or PBI-Premium server to connect.

### Step 7: Add to Power BI as External Tool

The last step is to copy the pbitools.pbitool.json file from the [VS Code Asset Folder](https://1drv.ms/u/s!AtDlC2rep7a-9WJOGr09KbK7Q3Zb?e=uoEMFH) to the following folder on your computer.

```
C:\Program Files (x86)\Common Files\Microsoft Shared\Power BI Desktop\External Tools
```

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/10/B6.jpg?resize=1000%2C356&ssl=1)

More detail on the details for the JSON file can be [found here](https://docs.microsoft.com/en-us/power-bi/create-reports/desktop-external-tools#how-to-register-external-tools) on how to register an external tool.

```
{
	"version": "1.0",
    "name": "PBI Tools",
	"description": "Launch PBI Tools to quickly and easily make batch changes to your semantic model.",
	"path": "C:\\VSCode\\ET1\\bin\\Debug\\netcoreapp3.1\\ET1.exe",
	"arguments": "%server%",
	"iconData": "data:image/jpeg;base64,/9j/4AAQ...<removed for brevity>."
}
```

The main property in the file is the “path” property, which in my case is pointing to the executable generated each time I debug the app. As I regularly update/change my copy of this app, this is my preference. But if you do compile and move the executable, remember to update the external tool JSON. Note the slash character \ needs to be *escaped* by entering it twice.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/10/B7.jpg?resize=900%2C266&ssl=1)

The “arguments” property passes the current port number to your VS Code app, which is picked up in the by the Main function.

## That’s it!

If you now open Power BI Desktop on your machine, you should now have a new icon in your external tools. My actual version is much longer and has tasks that you probably wouldn’t use, so I have only left a sample set of useful tasks to provide a framework.

My first preference is always to use the fantastic [DAX Studio](https://daxstudio.org/) and [Tabular Editor](https://tabulareditor.com/), but sometimes you need to perform a task that hasn’t quite found it’s way into these great tools.

I do enjoy using VS Code more and more these days. Not just for coding either. I have found it to be quite a powerful alternative to Notepad++.

A video walkthrough of this exercise can be [found here](https://youtu.be/AacRk2DRaWA) :

4
1
vote

Article Rating
