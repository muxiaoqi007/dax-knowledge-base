---
title: "Automatically create measures in Power BI using VS Code"
url: "https://dax.tips/2020/07/30/automatically-create-measures-in-power-bi-using-vs-code"
published: "2020-07-29"
updated: "2020-07-30"
source: "dax.tips"
---

My [[using-visual-studio-code-with-power-bi|last blog]] introduced the idea of using Microsoft Visual Studio Code to work with Power BI Models. For this article, I build on that idea by showing how you can use a TOM based script to automatically generate measures in your model Power BI (or Azure Analysis Services) model.

For simplicity, the example in this blog will do the following:

- Connect to an instance of Power BI Desktop
- Iterate through every Table in the model
- Iterate through every Column in the “current” table from the outer loop
- If the Column is numeric *and not hidden*, create a simple [Sum of <column>] measure

## The exercise

Create a new folder somewhere on your machine and open the folder using VS Code. I will assume you have performed the one-off tasks mentioned in Step 1 and Step 2 from my [[using-visual-studio-code-with-power-bi|previous blog]] to 1) install visual studio and 2) install the .Net Core SDK.

Open the newly created folder in VS Code and run the following two commands from a terminal window. These get explained as steps 3 & 4 in my [[using-visual-studio-code-with-power-bi|previous blog]].

```
dotnet new console
```

```
dotnet add package Microsoft.AnalysisServices.NetCore.retail.amd64 --version 19.6.0-Preview
```

These two commands create the shell project that we can add our TOM script.

Finally, add the following C# code into the Program.cs file.

```
using System;
using Microsoft.AnalysisServices.Tabular;

namespace AutoMeasure
{
    class Program
    {
        static void Main(string[] args)
        {
            Server server = new Server();
            server.Connect("localhost:54627");

            Model model = server.Databases[0].Model;

            foreach(Table table in model.Tables)
            {
                foreach(Column column in table.Columns)
                {
                    if(
                            (
                            column.DataType == DataType.Int64 ||
                            column.DataType == DataType.Decimal || 
                            column.DataType == DataType.Double 
                            )
                            && column.IsHidden==false)
                    {
                    
                        string measureName = $"Sum of {column.Name} ({table.Name})";
                        string expression = $"SUM('{table.Name}'[{column.Name}])";
                        string displayFolder = "Auto Measures";

                        Measure measure = new Measure()
                        {
                            Name = measureName ,
                            Expression = expression,
                            DisplayFolder = displayFolder
                        };
                        
                        measure.Annotations.Add(new Annotation(){Value="This is an Auto Measure"});

                        if(!table.Measures.ContainsName(measureName))
                        {
                            table.Measures.Add(measure);
                        }
                        else
                        {
                            table.Measures[measureName].Expression = expression;
                            table.Measures[measureName].DisplayFolder = displayFolder;
                        }
                    }
                }
            }
            model.SaveChanges();
        }
    }
}
```

Notes for the code:

Double click inside the code to enable select-all, copy/paste etc.

- Line 11 contains the connection string to the model. This connection string will need to be changed to suit your current model.
- Line 15 iterates through every Table in the model
- Line 17 iterates through every Column in the outer-loop
- Line 25 the operator should be && rather than the HTML code shown by the syntax code highlighter
- Line 29 is the actual DAX expression, so needs to be valid DAX
- Line 30 defines the name of the display folder for the auto measures
- Line 39 marks the auto measures with an annotation – which will make removing auto measures easier to perform
- Line 53 commits the changes to the model (if successful)

After running the code in Visual Studio (hit the F5 key), you can jump across to check your model (in my case Power BI Desktop) to see if the new measures appear.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/07/B1-1.png?resize=496%2C743&ssl=1)

As you can see, my Sales table now has a folder called Auto Measures, and this now has three new measures that can be used in reports.

Easy as that!

## Summary

The next steps are to compile the app and add to the new External Tools area in the Power BI Desktop ribbon. Once this gets added, you can hit the button to run the script automatically over your model to perform common bulk tasks with ease. I will show how to do this in my next blog.

Additional measures, based around other common patterns (MAX, MIN, AVERAGE) etc. should be easy enough to add.

Another idea (to be covered in a future blog) is to skip columns involved in relationships – as these columns probably don’t need auto measures.

As always, let me know how you get on. I enjoy hearing how people get on using tools like this in interesting ways.

4.8
6
votes

Article Rating
