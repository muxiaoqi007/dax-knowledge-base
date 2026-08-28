---
title: "Power BI and Regular Expressions"
url: "https://dax.tips/2017/05/23/power-bi-and-regular-expressions"
published: "2017-05-23"
updated: "2017-05-24"
source: "dax.tips"
---

I read a quote somewhere along the lines of…

```
If you are trying to solve a problem with regular expressions...
you now have two problems
```

This seems a little harsh for a useful tool that has been with us for what seems a very long time.  Plenty of languages offer support for RegEx string searching and pattern matching but not so far in Power BI Desktop.

I thought I would share a simple and quick way to enable the use of RegEx in Power BI.

This could be useful in a number of ways.  RegEx patterns could help validate strings from source systems:

- Valid email addresses
- URLs
- IP addresses
- Product Codes
- Stripping numbers or texts from strings

I’m going to look at the first scenario and demonstrate with a simple exercise how you might use RegEx to test if email addresses are valid.

DAX and M don’t have any dedicated functions for RegEx so the technique I’m sharing uses R Script in the Query Editor.

You will need to have an instance of R installed on your workstation and have your Power BI options configured for R.

Lets start with some data.  Here is a small sample of good and bad email addresses which I will use to add a column to display which email addresses are good or bad.

```
Email Address
----------------------
test@somedomain.com
another@emailaddress.com
not an email address
a b c@email.com
```

Once the data is loaded using the Enter Data function in Query Editor, click the Run R Script button on the Transform Tab.

![Transform Tab](https://i0.wp.com/dax.tips/wp-content/uploads/2017/05/transform-tab.png?resize=1000%2C109&ssl=1)

This opens the following R Script editor;

![R Editor](https://i0.wp.com/dax.tips/wp-content/uploads/2017/05/r-editor.png?resize=714%2C432&ssl=1)

Note that any line that starts with a # character will be ignored as a remark/comment line.

Next add the following code;

```
# 'dataset' holds the input data for this script
pattern <- "^[[:alnum:].-_]+@[[:alnum:].-]+$"
isValidEmail
```

and that’s it!

Lets have a brief look at what each line is doing.

The first line simply adds the Regular Expression pattern to a variable. This is for readability and also makes updating the pattern less likely to break the code.

The second line defines a function which will test any string passed against the pattern and return either TRUE or FALSE as appropriate.

The final line, **output** will return the updated dataset as a table.

The **within** function adds a new column/vector to the dataset.  Note the very first line in the script editor advises the input data is held in a variable called **dataset**.

**ValidEmail** is the column/vector we are adding and calls the **isValidEmail** function passing data from the Email column as a parameter.

Once added we should see the following output:

![final](https://i0.wp.com/dax.tips/wp-content/uploads/2017/05/final.png?resize=685%2C262&ssl=1)

We now have a new column called **ValidEmail** which shows TRUE/FALSE for each line depending on how the data in the Email column is matched with our regular expression pattern.

Using RegEx for validating email addresses is an interesting can of worms.  The characters allowed to be used in a valid RFC email address makes using RegEx for email validation complex.  Perhaps in this case ValidEmail could be used as a warning.

Hopefully this might be useful if you are considering using regular expressions in your Power BI report. Even if not, it shows a method of adding columns using R Script to your dataset.

Feel free to download the PBIX file from here

<https://1drv.ms/u/s!AtDlC2rep7a-kCO2CnoREgQ9mxV0>

5
3
votes

Article Rating
