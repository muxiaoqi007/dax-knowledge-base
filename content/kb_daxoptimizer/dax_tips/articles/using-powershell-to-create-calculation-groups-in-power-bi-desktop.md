---
title: "Using PowerShell to create Calculation Groups in Power BI Desktop"
url: "https://dax.tips/2022/06/20/using-powershell-to-create-calculation-groups-in-power-bi-desktop"
published: "2022-06-20"
updated: "2022-06-20"
source: "dax.tips"
---

![](https://i0.wp.com/dax.tips/wp-content/uploads/2022/06/744577681-huge-1200x800-1.jpg?resize=1000%2C667&ssl=1)

## Background

I [recently shared on Twitter](https://twitter.com/PhilSeamark/status/1534681534338564096) a 7-module learning path on MS Learn that teaches all you need to know about calculation groups in Power BI. This learning path is an excellent course, and I highly recommend it. However, as part of this exchange, I received a reply from someone lamenting that calculation groups are unusable in organisations that will not allow non-Microsoft applications. Power BI Desktop does not currently have UX enabling you to create/manage calculation groups in a Power BI Model, so the most common method today is to use 3rd party tools such as Tabular Editor.

This exchange is not the first time I have heard this feedback, so I decided to share a technique showing how you *can* use Microsoft tools. The approach used in this article uses PowerShell but can quickly get translated to VS Code or other scripting environments.

## The Technique

To use PowerShell to create/manage calculation groups, you need the following:

- A DLL file stored locally on your machine to reference for the Tabular Object Model
- Power BI Report open in Power BI Desktop.
- PowerShell Script

### Locate DLL file

The first item is a DLL stored on your machine to be used by PowerShell to work with Power BI Desktop. This file is created and owned by Microsoft and is designed to allow tools to work with Power BI Models. The name of the DLL file **Microsoft.AnalysisServices.Tabular.dll** may already be on your local if you have [SSMS installed](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver16).

If you don’t have SSMS installed on your local machine, or any other Microsoft tool that installs the **Microsoft.AnalysisServices.Tabular.dll** file somewhere, you can grab it from [Nuget](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64) by searching for Microsoft.AnalysisServices and [downloading the latest version](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64) of the **Microsoft.AnalysisServices.retail.amd64** package. The package should only be about 5Mb in size and will download a file with a .nupkg filename extension. Rename the downloaded file to a .Zip filename extension so it can open using your favourite zip tool. The **Microsoft.AnalysisServices.Tabular.dll** file can be found in the \lib\net45 subfolder, where it can copy to a location somewhere on your local machine. Take note of the location used as the destination of the file so you can update the PowerShell script.

### Open Power BI Model

For PowerShell to connect to Power BI Desktop, it needs to understand what port Power BI Desktop has open for API calls used in our PowerShell script. Power BI Desktop uses a different port each time it opens, so this value cannot be hardcoded into our PowerShell script. The PowerShell script has a couple of lines of code to derive this value from operating system processes. For simplicity, the PowerShell script is kept simple and only works so long as you only have a single copy of Power BI Desktop open.

Multiple instances of Power BI Desktop will confuse the version of the PowerShell script used in this blog. The PowerShell script can be modified to support multiple instances of Power BI Desktop, but I decided to leave that out of scope for now.

### The PowerShell Script

Open the following script in **PowerShell ISE** (Integrated Scripting Environment)

```dax
##############################################################################################
## 1. Create reference to Analysis Services DLL in SSMS path
##############################################################################################


    # Approach 1
    # Use the DLL in the GAC if you already have SSM, or other tool installed

    <#
    try {
        $Result = [System.Reflection.Assembly]::LoadWithPartialName('Microsoft.AnalysisServices.Tabular')
        }
    catch  { 
        $_.Exception.LoaderExceptions 
        }
    #>

    # Approach 2
    # Create a reference directly to a file called Microsoft.AnalysisServices.Tabular.Dll already installed by SSMS

    <#

    #$assemblypath = "c:\program files (x86)\microsoft sql server management studio 18\common7\ide\microsoft.analysisservices.Tabular.dll"
    try {add-type -path $assemblypath}
    catch  { $_.exception.loaderexceptions }

    #>

    # Approach 3
    # Download and extract the DLL From Nuget to somewhere local on your machine

    $assemblypath = "c:\temp\microsoft.analysisservices.Tabular.dll"
    try {add-type -path $assemblypath}
    catch  { $_.exception.loaderexceptions }


##############################################################################################
## 2. Get Power BI Desktop Port Number
##############################################################################################


    $ProcessName = 'msmdsrv'
    try {
        $Process = Get-Process -Name $ProcessName -ea Stop -ErrorVariable 'ps_error'
    } catch {
        throw "Warning: Could not find any process named: '$ProcessName'.`nMake sure Power BI is open."
    }
    $Result = Get-NetTCPConnection -OwningProcess $Process.Id
    $Port = $Result | Select-Object -ExpandProperty LocalPort | Sort-Object -Unique
    $ConnectionStringPBIDesktop = "localhost:$Port"


##############################################################################################
## 3. Create AS Server object and Connect to Power BI 
##############################################################################################

    $Server = [Microsoft.AnalysisServices.Tabular.Server]::new()
    $Server.Connect($ConnectionStringPBIDesktop)
    $Model = $Server.Databases[0].Model

    <#
    foreach($table in $Model.Tables)
    {
        $table.Name
    }
    #>

##############################################################################################
## 4. Remove Existing Calculation Group
##############################################################################################


    $result = $Model.Tables.Remove($Model.Tables["Time Calc"])


##############################################################################################
## 5. Create Calculation Group
##############################################################################################


    $CalculationGroup = [Microsoft.AnalysisServices.Tabular.CalculationGroup]::new()
    $CalculationGroup.Description="None"
    $CalculationGroup.Precedence=10


##############################################################################################
## 6. Create Calculation Item
##############################################################################################


    $CalculationItem = [Microsoft.AnalysisServices.Tabular.CalculationItem]::new()
    $CalculationItem.Description = "YTD"
    $CalculationItem.Name = "YTD"
    $CalculationItem.Expression = "CALCULATE(SELECTEDMEASURE(),DATESYTD(DimDate[FullDateAlternateKey]))"
    $CalculationItem.Ordinal = 0

    $CalculationGroup.CalculationItems.Add($CalculationItem)


##############################################################################################
## 7. Create Calculation Item 2
##############################################################################################


    $CalculationItem = [Microsoft.AnalysisServices.Tabular.CalculationItem]::new()
    $CalculationItem.Description = "MTD"
    $CalculationItem.Name = "MTD"
    $CalculationItem.Expression = "CALCULATE(SELECTEDMEASURE(),DATESMTD(DimDate[FullDateAlternateKey]))"
    $CalculationItem.Ordinal = 0

    $CalculationGroup.CalculationItems.Add($CalculationItem)


##############################################################################################
## 8. Create Table
##############################################################################################


    $Table = [Microsoft.AnalysisServices.Tabular.Table]::new()
    $Table.Name = "Time Calc"
    $Table.CalculationGroup = $CalculationGroup


##############################################################################################
## 9. Create Table Column
##############################################################################################


    $TableColumn = [Microsoft.AnalysisServices.Tabular.DataColumn]::new()
    $TableColumn.Name = "Time Calculations"
    $TableColumn.DataType = "String"

    $result = $Table.Columns.Add($TableColumn)


##############################################################################################
## 10. Create Partition
##############################################################################################


    $Partition = [Microsoft.AnalysisServices.Tabular.Partition]::new()
    $Partition.Name = "Partition For Time Calc"
    $Partition.Source =[Microsoft.AnalysisServices.Tabular.CalculationGroupSource]::new()

    $result = $Table.Partitions.Add($Partition)


##############################################################################################
## 11. Add Table to Model
##############################################################################################


    $result = $Model.Tables.Add($Table)
   

##############################################################################################
## 12. Commit all changes to the model
##############################################################################################

    $result = $Model.DiscourageImplicitMeasures=[bool] 1
    $result = $Model.SaveChanges()


##############################################################################################
## Woot!
##############################################################################################

```

The PowerShell script is broken into several steps with comment blocks describing the purpose of each section.

The first two are the most critical sections, designed to connect the script to an instance of Power BI Desktop. Once connected, the rest of the logic should be easy enough to follow. Objects like CalculationGroups, CalculationItems, a Table and Partition need to be named to suit and added to the model (which the script does for you).

The script can be run multiple times without breaking, allowing you to iterate the script making changes to object names, DAX expressions etc. Each time you run the script in PowerShell, you will need to jump over to Power BI Desktop and click a button to refresh the model structure to load the changes.

## Script Commentary

A more detailed explanation of each step of the script is as follows.

1. Create reference to Analysis Services DLL in SSMS path  
   - There are three approaches shown in this section for referencing the **Microsoft.AnalysisServices.Tabular.dll**. Only one of the three approaches should be uncommented here.
   - My personal preference is Approach 1, which uses the [System.Reflection.Assembly] notation to locate the DLL file in the GAC (c:\windows\Microsoft.Net\assembly\GAC\_MSIL folder). But this relies on having an application like SSMS install the DLL to the GAC during an install process.
   - The Nuget approach works pretty well and doesn’t rely on needing Admin privileges to get working if you can’t find the **Microsoft.AnalysisServices.Tabular.dll** file anywhere on your machine.
   - If you have a copy of **Microsoft.AnalysisServices.Tabular.dll** on your machine, it needs to be recent enough to include knowledge of what calculation groups are.
2. Get Power BI Desktop Port Number
   - The code in this block tries to identify the listening port of your Power BI Desktop instance. In this form, it only works if you have only one copy of Power BI Desktop open.
   - The logic searches for a process called msmdsrv, which happens to be the Analysis Services engine started by Power BI Desktop. Once it has the Process ID, it can search for the port number via the Get-NetTCPConnection commandlet.
   - The final connection string will look like “localhost:50555” and gets used in step three to connect.
3. Create AS Server object and Connect to Power BI
   - This code block attempts to connect to the instance of Power BI Desktop. If the connection succeeds, the rest is easy.
   - A commented section of PowerShell code shows how you can iterate every Table in the model and display a list of table names on the screen.
4. Remove Existing Calculation Group
   - The single line in this block removes any existing Calculation Group in the model called “Time Calc”.
   - It doesn’t matter if you run this for the first time and there is no Calculation Group with that name.
5. Create Calculation Group
   - Here we use the PowerShell [object]::new() syntax to construct a new Calculation Group object.
   - Only two properties get set in this example, but additional properties can get set as needed.
   - This code does not yet add the Calculation Group to the model. That work is completed later in the script.
6. Create Calculation Item
   - A new calculation item is created here using the [object]::new() method.
   - Once created, properties can get set, such as Name and Expression.
   - The Expression needs to be valid DAX syntax.
   - The last line in this block adds the newly created CalculationItem to the CalculationGroup object created in step 5.
7. Create Calculation Item 2
   - This step shows how you can add additional calculation items using a similar approach to step 6
   - Make sure you choose a different value for the Name property.
8. Create Table
   - Before adding a calculation group to our model, we need to create a new table object to host the calculation group.
   - The value used for the Name property here should match the same value used in the “remove existing calculation group” in step 4.
   - The last line of code in this block is where our calculation group created in step 5 gets added to the model.
9. Create Table Column
   - The Table object created in step 8 used to host our calculation group must have a column.
   - A new column object is created here and added to the Table object created in step 8
   - This block should not need to be changed.
10. Create Partition
    - The Table object created in step 8 also needs to have a partition, so this code does the minimal amount required to create the needed partition required.
    - The last line of code adds the partition to the table.
    - This block should not need to be changed.
11. Add Table to Model
    - The single line of code here adds the Table object created in step 8 to the model.
    - This step also adds the Table Column, Partition, CalculationGroup and any CalcuationItems to the model.
    - This block should not need to be changed.
12. Commit all changes to the model
    - This block finally commits all changes to the model.
    - A dialog in Power BI Desktop will show that changes have been made and require you to click a button to refresh the model structure (not the same as a data refresh!)

And that’s it. The pattern is hopefully easy enough to follow – and once set up, it shouldn’t take much longer to create a calculation group than using a 3rd party tool.

## Summary

Hopefully, this template helps if you cannot install/use non-Microsoft tools like Tabular editor to work with calculation groups. The technique is not limited to only adding calculation groups to Power BI Desktop and can be used to perform all manner of tasks via the Tabular Object Model, such as creating measures, documenting models, querying data etc.

PowerShell is an excellent tool for automating tasks and works well if your model gets hosted in a Power BI Premium workspace.

The technique can be enhanced in many different ways. The script could be run as an external tool from Power BI Desktop. Running as an external tool means Step 2 could be simplified.

Thanks to Paul on Twitter for giving me the idea, and Kasper for [providing the c# example](https://www.kasperonbi.com/adding-calculation-groups-using-the-tabular-object-model/) I ported to PowerShell for this article.

5
4
votes

Article Rating
