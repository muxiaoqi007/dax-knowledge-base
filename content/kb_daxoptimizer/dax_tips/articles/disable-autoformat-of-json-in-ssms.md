---
title: "Disable auto-format of JSON in SSMS"
url: "https://dax.tips/2020/10/21/disable-autoformat-of-json-in-ssms"
published: "2020-10-20"
updated: "2020-10-20"
source: "dax.tips"
---

If you ever work with [TMSL](https://docs.microsoft.com/en-us/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) based scripts (basically big chunks of text in JSON format) in SQL Server Management Studio (SSMS), you may find that SSMS sometimes auto formats your JSON after you paste some script into the Text Editor. This can be mildly frustrating, but also potentially dangerous if elements are stripped without you knowing from parts of your JSON script.

This can happen when you either :

- Paste a block of JSON script into SSMS
- Paste a small item of text into some existing JSON
  - The item you paste does not have to be JSON, such as some text for a property.

Previously, I after pasting I would use CTRL-Z to undo the destructive auto-formatting 1 level, but retain the values just pasted to the editor.

### The Solution

While you are technically working with JSON text, SSMS believes you are using XML in your XMLA window. Open Tools -> Options -> Text Editor -> XML -> Formatting and clear the **On paste from clipboard** checkbox

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/10/B1-1.jpg?resize=1000%2C685&ssl=1)

And that’s it!

The animation below shows a small section of JSON being pasted into SSMS twice. The first time shows what happens with default settings. The JSON text is “auto-formatted” to remove all the tab characters. The second time the same JSON script is pasted after the SSMS option is fixed.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2020/10/ani.gif?resize=970%2C592&ssl=1)

Don’t forget you can also paste JSON text into [[use-visual-studio-code-to-autoformat-json|Visual Studio Code]] and use the CTRL-A, then SHIFT-ALT-F to auto format your JSON code so long as you set the language mode to JSON using the option on status bar (bottom right).

0
0
votes

Article Rating
