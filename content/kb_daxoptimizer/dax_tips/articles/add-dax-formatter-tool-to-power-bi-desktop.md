---
title: "DAX Formatter tool for Power BI Desktop"
url: "https://dax.tips/2020/11/23/add-dax-formatter-tool-to-power-bi-desktop"
published: "2020-11-22"
updated: "2020-12-02"
source: "dax.tips"
---

Last week I was honoured to take part in the latest edition of the [Power BI Dev Camp](https://powerbidevcamp.powerappsportals.com/sessions/session04/) which is run by my colleague Ted Patterson. It was a fun session which I enjoyed.

As part of the Dev camp, I walked through some of my recent Visual Studio Code based blog posts on how to perform various tasks against models hosted in Power BI desktop.

While preparing for the session, Ted and I agreed that it might be helpful to create a small external tool that could automatically format all DAX expressions in a Power BI model. The idea is to leverage the excellent [DAX Formatter](http://www.daxformatter.com/) API provided by the good folks at SQLBI. This API is the same endpoint used when you format your DAX using [DAX Studio](https://daxstudio.org/).

### Build your own using Visual Studio Code

This blog shows how you can create your own DAX Formatting tool from scratch using Visual Studio Code. You can then add the tool to the External Tools button on the Power BI Ribbon so that all your DAX expressions can be *beautified* at the single press of a button.

### HASHCODE

I’m conscious that this is a free endpoint, so wanted to make sure any helper tool does not overload the API unnecessarily, so designed the script in a way that reduces the number of times the API gets called.

The idea is to store a HASHCODE value that represents the DAX Expression in an Annotation. An [Annotation](https://docs.microsoft.com/en-us/dotnet/api/microsoft.analysisservices.tabular.annotation?view=sqlserver-2016) in Analysis Services is a place you can store items of text that aren’t directly involved in the calculation but can be useful for other reasons – often to help with documentation.

If a DAX expression, such as a measure, calculated column, or calculated table does not have an annotation, we send the text to the API and receive back some formatted DAX. We create a HASH value of the formatted text and store this in the annotation.

The next time the script runs, we create the HASH value from the annotation and compare it with the HASH value over the current expression. If the two values match, the DAX expression is still formatted, and there is no need to send it off to the API again.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/11/b1.jpg?resize=602%2C664&ssl=1)

### The Script

To get this up and running, you need to complete the following steps.

- Install .[Net Code SDK](https://dotnet.microsoft.com/download/visual-studio-sdks) (version 3.1 or version 5.0)
- Install the latest copy of [Visual Studio Code](https://code.visualstudio.com/)
- Create a blank project folder and open in Visual Studio Code
- Create project files by running following command in the terminal window
  - dotnet new console
- Install the required packages
  - [AMO Client Libraries](https://www.nuget.org/packages/Microsoft.AnalysisServices.NetCore.retail.amd64) (updated!!)
  - [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)
- Copy ALL code from the Program.cs file from the [asset folder](https://1drv.ms/u/s!AtDlC2rep7a-9iuaVebY6ZrqnARr?e=mGVso7) into your copy of the program.cs file
- Run the script against a PBIX file open in Power BI Desktop
  - Be sure you have updated the model to V3
  - Update the port number at line 16 for testing (not required if launched as External Tool)
- Copy the External Tool JSON file from the [asset folder](https://1drv.ms/u/s!AtDlC2rep7a-9iuaVebY6ZrqnARr?e=mGVso7) to the [External Tools folder](https://docs.microsoft.com/en-us/power-bi/create-reports/desktop-external-tools#how-to-register-external-tools) on your machine.
  - Open, update and edit the path property to point to where ever your app is created.

### The Video

### Summary

All the steps required to get this up and running are the same as my recent blog posts. The only different thing is the code pasted into the Program.cs file – and the packages required to get installed to the project.

The main point of this blog is not to provide you with a long-lasting tool, instead to show you how you can solve interesting use cases with the Tabular Object Model (TOM) and Visual Studio Code.

Big thanks to Ted Pattison for helping me on this as well as SQLBI for providing an excellent API endpoint.

If you don’t want to mess with Visual Studio Code and just want the External tool, simply grab the net5.0.zip file from the Asset folder and extract to a folder on your Windows 10 machine. You’ll need the DAXFormatter.pbitool.json file as well (don’t forget to update the path property). You’ll possibly also need to install the .net 5.0 runtime if it doesn’t work.

If you need to tweak the code to support localization, adjust the JSON payload posted to the API endpoint at line 180.

Another tweak could be to connect to existing models in Azure Analysis Services, or to models hosted in Power BI Premium. All you need to do is update the connection string and run locally in Visual Studio Code.

5
8
votes

Article Rating
