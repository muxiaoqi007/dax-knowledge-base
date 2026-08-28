---
title: "DAX : Pivot Text into a List of Words"
url: "https://dax.tips/2019/07/05/dax-pivot-text-into-a-list-of-words"
published: "2019-07-05"
updated: "2019-07-07"
source: "dax.tips"
---

![I1](https://i0.wp.com/dax.tips/wp-content/uploads/2019/07/i1.png?resize=651%2C554&ssl=1)

### Introduction

For this blog, I look at a method to take some text in the form of a horizontal sentence and use DAX to pivot the text into a vertical list of words.

The PBIX file for this exercise can is available [here](https://1drv.ms/u/s!AtDlC2rep7a-xBdRyrtc-uEOn5q5?e=ZLRfna).

There is no current native function in DAX that can take a string of text and output a list of words – **mostly because this can be done easily using other tools, such as Power Query**.  Please note, there is a much faster/smaller version at the bottom of the blog as a [[dax-pivot-text-into-a-list-of-words|bonus item]].

Other languages offer functions such as SPLIT to take a set of text and generate an array or list of individual words. I’m not suggesting this is a significant gap in the DAX language, as this is not a common ask I see on my travels, but it does present an interesting challenge, and this solution shows how you can mix techniques such as:

- Using the GENERATE() function to pivot
- Nested Variables
- Using CONCATENATEX to unpivot

Here is what I came up with:

### Step 1: Generate some data to work with.

For this blog, I use the following DAX to generate a simple two-row calculated table:

```
Table = 
    DATATABLE(
        "ID" , INTEGER ,
        "Text" , STRING ,
        {   
            { 1 , "The quick brown fox jumps over the lazy dog" } ,
            { 2 , "Happy Birthday" }
        }
    )
```

This calculated table generates a table that as follows:

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/07/I2.png?resize=455%2C96&ssl=1)

### Step 2: Write some DAX to split the data

The first step I take is to use the GENERATE() function to pivot each sentence into a table that contains 1 row per letter. The idea here is to figure out where the space characters are so to help in the next step:

```dax
Words = 
VAR SplitCharCode = UNICODE(" ")

VAR Table1 =
    ADDCOLUMNS(
        GENERATE(
            'Table',
                VAR TextLength = LEN('Table'[Text])
                RETURN 
                    GENERATESERIES(1,TextLength)
                ),
            "Letter",
            UNICODE(MID([Text],[Value],1))
        )
RETURN Table1

```

In the above DAX, the GENERATE function is doing a CROSS JOIN between the original table, and a dynamically created numbers table (GENERATESERIES) to pivot each sentence into 1 row per letter. The individual letters are extracted using the MID function before being converted to ASCII numbers by the UNICODE function to preserve Upper/Lower case.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/07/I3.png?resize=692%2C431&ssl=1)

The right-most column called [Letter] includes a list of ASCII numbers that represent each individual character from the text being pivoted.

### Step 3: Identify and count the number of spaces

The next layer of DAX code looks for rows that represent a space character (ASCII code = 32) and add a cumulative total column that counts the number of spaces.

```dax
VAR Table2 = 
    ADDCOLUMNS(
            Table1,
            "Word Number", 
            VAR myID = [ID] 
            VAR myValue = [Value]
            RETURN 
                CALCULATE(
                    COUNTROWS(
                        FILTER(
                            Table1,
                            [Letter] = SplitCharCode 
                            && [ID] = myID
                            && [Value] < myValue
                            )
                        )+1)
                    )

```

The new [Word Number] column appears as follows:

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/07/I4.png?resize=849%2C435&ssl=1)

### Step 4: Unpivot the letters back but break at each space

The final step is to unpivot the data back to horizontal format, but only where there is a space character.

```dax
RETURN
    SUMMARIZE(
        Table2 ,'Table'[ID] , [Word Number] ,
        "Word" , 
            VAR myID = [ID] 
            VAR myWordNumber = [Word Number] 
            VAR myWord = 
                CONCATENATEX(
                    FILTER(
                        Table2,
                        [Word Number]=myWordNumber 
                        && [ID] = myID),UNICHAR([Letter])
                        )
            RETURN TRIM(myWord)
        )

```

The SUMMARIZE function creates a distinct list of [ID] and [Word Number] rows, and then for each row, the appropriate individual letters are assembled using the CONCATENATEX function and converted back from ASCII to the text version.

The final format could get analysed several ways, and while this is not a common requirement. I thought it was an interesting exercise of data manipulation in DAX.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/07/I5.png?resize=396%2C385&ssl=1)

The final DAX in full is as follows:

```dax
Word List = 
VAR SplitCharCode = UNICODE(" ")

VAR Table1 =
    ADDCOLUMNS(
        GENERATE(
            'Table',
                VAR TextLength = LEN('Table'[Text])
                RETURN 
                    GENERATESERIES(1,TextLength)
                ),
            "Letter",
            UNICODE(MID([Text],[Value],1))
        )

        
VAR Table2 = 
    ADDCOLUMNS(
            Table1,
            "Word Number", 
            VAR myID = [ID] 
            VAR myValue = [Value]
            RETURN 
                CALCULATE(
                    COUNTROWS(
                        FILTER(
                            Table1,
                            [Letter] = SplitCharCode 
                            && [ID] = myID
                            && [Value] < myValue
                            )
                            
                            )+1)
                        )
RETURN
    SUMMARIZE(
        Table2 ,'Table'[ID] , [Word Number] ,
        "Word" , 
            VAR myID = [ID] 
            VAR myWordNumber = [Word Number] 
            VAR myWord = 
                CONCATENATEX(
                    FILTER(
                        Table2,
                        [Word Number]=myWordNumber 
                        && [ID] = myID),UNICHAR([Letter])
                        )
            RETURN TRIM(myWord)
        )

```

### Bonus Item (faster/cleaner approach)

As with most exercises in programming, there are always other ways to solve a problem. A much faster and cleaner alternative that was sent to me by Simon Nuss which uses the [PATH](https://docs.microsoft.com/en-us/dax/path-function-dax) set of Parent/Child functions in DAX.

The version he sent is as follows:

```dax
VAR SplitByCharacter = " "
VAR Table0 =
    ADDCOLUMNS (
        GENERATE (
            'Table',
            VAR TokenCount =
                PATHLENGTH ( SUBSTITUTE ( 'Table'[Text], SplitByCharacter, "|" ) )
            RETURN
                GENERATESERIES ( 1, TokenCount )
        ),
        "Word", PATHITEM ( SUBSTITUTE ( 'Table'[Text], SplitByCharacter, "|" ), [Value] )
    )
RETURN
    Table0

```

I tested this against the ‘Description’ text in a 280,000 row table from the Wide World Importers database and this version took less than half a second to complete, compared with approximately 30 seconds using the CONCATENATEX approach over the same data. Pretty impressive!

The reality for this particular exercise is you are probably much better to execute this manipulation upstream in your ETL process and not in DAX. However, these approaches both demonstrate how you can layer your code in DAX to solve interesting challenges.

5
3
votes

Article Rating
