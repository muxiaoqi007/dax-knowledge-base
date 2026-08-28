---
title: "DAX : Approx Distinct Count"
url: "https://dax.tips/2019/12/20/dax-approx-distinct-count"
published: "2019-12-19"
updated: "2019-12-20"
source: "dax.tips"
---

## Introduction

For this blog, I want to share an interesting approach that can potentially speed up slow reports that include a distinct count calculation.

The approach described in this article explains how you can add a DIY version of an *approximate* distinct count calculation to a DAX based model. The method combines a little pre-processing with some cool mathematical shortcuts to provide a result that is close to the exact amount, and up to 25x faster!

The table below shows the performance gain I recorded over five quite different queries. The best performance gain in the top row improved a 173-second query (nearly 3 mins!) down to under 7 seconds. The accuracy of the approximation was less than a quarter of a percent.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/12/B18.png?resize=579%2C135&ssl=1)

DAX based Approx Distinct Count (HLL) versus DISTINCTCOUNT (Baseline)

To apply this technique to your model, you need to add two additional integer columns (explained in more detail below) along with a calculated measure for every core column you’d like to speed up.

## The challenge

As you may know, measures involving a Distinct Count calculation are often the slowest and most resource-hungry measures in your models. The reason is the extra work required by the engine to produce an accurate distinct value compared with other aggregations such as SUM, MAX and COUNT etc.

If a sales table shows ten transactions per day for a week, a SUM over the QTY column involves a single scan, and only a small amount of memory is required to store and accumulate a value during a single scan. The COUNT function is similar to the SUM in that it only requires a small amount of memory and can also be calculated by a single scan operation.

Whereas for a distinct count over a Customer ID column, an internal process needs to keep track of customers already counted to avoid double-counting. The process requires more memory along with additional data processing at runtime. This compounds as the number of distinct values grow.

In DAX we have the [DISTINCTCOUNT](https://docs.microsoft.com/en-nz/dax/distinctcount-function-dax) function, which provides a result which is 100% accurate. There are many scenarios where this is important, such as finance, auditing, healthcare etc. However, there are also scenarios where it might be more relevant to prioritise speed over precision – especially when working with large values.

We can also write a precise, distinct count calculation in DAX using other tricks which involve combining various functions such as SUMX, SUMMARIZE, VALUES or DISTINCT etc. These all perform better (or worse) depending on the underlying nature of the data – but can still quite resource-intensive on CPU & memory.

## Approximate Distinct Count

If I search for “DAX Distinct Count” in Google, I quickly get back a result telling me it found approximately 248,000 results (in 0.36 seconds). The fast result time is far more useful to me than taking 10 minutes or more to say to me it found exactly 248,123 results. In this scenario, the 248,000 number is good enough.

There are plenty of other reporting scenarios where you might prefer a faster query time for your distinct count calculation, and still be happy so long as a result was close enough (say +/- 2%).

In December 2018, we introduced a new DAX function called [APPROXIMATEDDISTICNTCOUNT](https://docs.microsoft.com/en-nz/dax/approximate-distinctcount-function-dax) which, as the name suggests, doesn’t return a precise result; instead, it takes some mathematical shortcuts to return a result faster using less CPU and memory.

The catch with the APPROXIMATEDDISTINCTCOUNT function is it only works in Direct Query mode over an Azure SQL DB, or Azure SQL DW. The function cannot be tuned and simply makes use of the [APPROX\_COUNT\_DISTINCT](https://docs.microsoft.com/en-us/sql/t-sql/functions/approx-count-distinct-transact-sql?view=sql-server-ver15) function in TSQL.

This DAX function works if you are already using Direct Query with one of these data sources. Still, I wanted to see if I could replicate the function inside a data model – so had a crack at building a DIY version of APPROX DISTINCT COUNT function in DAX.

## DIY Approx Distinct Count

My initial research for a pattern quickly let me to the [HyperLogLog](https://en.wikipedia.org/wiki/HyperLogLog) (HLL) algorithm, which looked to be what I was after. The idea behind HLL is to quickly produce a cardinality *estimate* over a big dataset without consuming a large amount of memory. Variations of the HLL algorithm are used by companies such as Google, Facebook and Twitter to help them calculate, analyse and understand their massive datasets more efficiently.

There are many useful blog posts on the topic, which helped me slowly understand how the algorithm worked, so I could then find a practical way to implement it in DAX. Some of the articles focused just on theory, while others offer practical code-based solutions in a variety of programming languages.

I post the full [list of articles](#references) I found at the bottom of this blog, but the most useful link for me is the following:

<http://static.googleusercontent.com/media/research.google.com/en/us/pubs/archive/40671.pdf>

The idea of HLL is first to create a HASH value of each item you plan to target in a distinct count calculation. Hashing values is often performed for encryption/decryption. However, for HLL, the primary objective of hashing items is to produce an output value that has the same number of bits for every input value.

| Text Value | Hash Value |
| --- | --- |
| DAX | 4172989e6bf5b674bd2a7a6dc770790b |
| Approximate | b81a30c12698563b79179ec37d43629b |
| DAX is Fun | de54835215117a88ba9e66967015f007 |
| 1 | c4ca4238a0b923820dcc509a6f75849b |
| 2 | c81e728d9d4c2f636f067f89cc14862c |
| 3 | eccbc87e4b5ce2fe28308fd9f2a7baf3 |

As you see in the table above, the HASH version of each text value is always the same length. Similar values like 1, 2 & 3 produce quite different HASH results. Other algorithms can get used to HASH in place of MD5 such as SHA1, SHA256 or MURMUR3. The most important characteristics for a hashing algorithm used for HLL is the algorithm always produces the same output for a given input, and the length of the output is always the same for all input value regardless of the length of the input value.

In this case, the 32 characters generated by the MD5 HASH are hexadecimal pairs that can get converted to a binary number which is 128 bits long.

The string of bits (1’s and 0’s) can then be used by the HLL process to help perform its magic.

Once we have a binary representation of a HASH value, we can pull out two important values from within each set.

- The HLL Bucket
- A count of consecutive zeros from a consistent point in the binary string

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/12/B1-1.png?resize=957%2C600&ssl=1)

A simple example showing 15 items (1 row per item) deriving integer values for Bucket and Zeros

The image above shows a block of binary values where each row represents an item processed in a distinct count calculation. The original value is converted to a HASH value using MD5, and the image shows a section of the binary representation. Usually, this would be 128 bits long, but this image shows a subset of these.

An imaginary line is placed vertically through all the values. In this case, it is about halfway along. Three columns to the left of the red-line get used to categorise each value into a hash bucket. This example uses only three columns for simplicity, which allows up to 8 buckets. Of the 15 items in this example, 1 item is in bucket 0, 1 item is in bucket 1, and so on up to bucket 7 which as 4 items.

The binary bucket value is converted back to decimal and show in the right-hand column under the Bucket heading. In the first row, the 111 value converts to bucket 7, while the second row, the 000 value converts to bucket 0. Using this technique, all rows get assigned to one of 8 buckets.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/12/B2.png?resize=875%2C227&ssl=1)

How to determine which bucket each row gets allocated. The top item is in bucket 7.

For the next part of the calculation, we count the number of consecutive zeros from a consistent point. In this case, the bits to the right of the vertical red line get used.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/12/B3-1.png?resize=799%2C239&ssl=1)

How to determine the values for the Zeros column. The top item has 5 consecutive values so will store a 5 in the Zeros column

The top row has 5 consecutive zeros to the right of the red-line, so we store a value of 5 for this row.

Once all Bucket and Zero values get extracted from the binary data, the HLL process can aggregate to produce a table with only 1 row per bucket along with a column showing the MAX over the Zeros column.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/12/B4.png?resize=255%2C320&ssl=1)

Aggregate table with 1 row per bucket, along with the MAX value from the Zeros column.

The image above shows that for all rows which happened to be in bucket 7, the maximum number of consecutive zeros was 5, while for bucket 5, the equivalent value is 3.

The final part of the HLL calculation is to exponentiate the value in the **MAX Zero** column by the power of 2 and then average the results.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/12/B5.png?resize=510%2C424&ssl=1)

HLL estimates 7 unique values by applying an average over the MAX Zeros data once they have been

In the image above, the table on the left has 1 row per bucket with the maximum number of consecutive zeros for each bucket. The table on the right shows the **MAX Zero** value to the power of 2. An average of these values produce a value of 7 as our estimate in this case.

#### Tuning

This example is designed to be overly simplistic to demonstrate the approach of HLL, and the result is probably not within +/- 2%. The reality is you can tune the algorithm by selecting a higher number of bits (and therefore buckets), along with the technique used to perform the average.

The example described in the [HyperLogLog in Practice](http://static.googleusercontent.com/media/research.google.com/en/us/pubs/archive/40671.pdf) paper uses 14 bits which results in 16,384 buckets (214 = 16,384), which might sound like a lot, but if you have many millions of rows to process, it starts to make more sense. More bits will produce better accuracy at the cost of some performance.

Other opportunities to tune the algorithm are to tweak the calculation that performs the final average. Some papers suggest discarding a percentage of high/low outliers to improve accuracy along with applying an offset for lower cardinality values. Other options include using variations on the final average, such as using a “[harmonic mean](https://en.wikipedia.org/wiki/Harmonic_mean)” in place of a more standard average. Another improvement is to apply a bias as part of the calc. Tweaks like these need to be tested to find the right balance between improving accuracy and slowing down the query speed.

The takeaway here is you *can* still tune the algorithm when you build it yourself, compared with using the options like the [APPROXIMATEDDISTCINTCOUNT](https://docs.microsoft.com/en-nz/dax/approximate-distinctcount-function-dax) function.

## Practical Implementation

Now we have the basic idea of how HLL works; the next section shows one way this can get implemented in DAX models. The solution I came up with is to simply add two integer columns to each table for each column you’d like to produce an approximate distinct count over. The values stored in these two columns need to be pre-calculated. Once the target column gets identified (say UserID), create a HASH value and then extract the bucket value and number of consecutive zeros from a consistent point. The HASH value can be then be discarded, with only the two integer values stored in the final table.

This process to generate the HASH and then extract the two integer values *could* be done while loading the data – either in PQ or TSQL, but given these aren’t dynamic, the best approach is to pre-calculate and store these values in the source table. Depending on how many bits are used (to control precision) for the bucket column, you’ll only be storing a number between 0 and 16,384, while the MAX Zero column will only have an integer between 0 and probably less than 30. These smallish integer values play nicely with columnstore based storage.

The following TSQL example generates the two required columns over a UserID column. The structure of the TSQL example uses an inner derived table that invokes the TSQL MD5 function to convert each UserID to a hexadecimal representation of the HASH. The outer SELECT statement parses the **md5** column to extract the hash bucket (HLL\_HashBucket) and the count of consecutive zeros (HLL\_FirstZero). Note the MD5 value does not end up in the final SELECT.

```
CREATE TABLE [fact].[MyEvents]
WITH
(
	DISTRIBUTION = HASH ( [DateKey] ),
	CLUSTERED COLUMNSTORE INDEX
)
AS
	SELECT 

		[DateKey] ,
		[UserID] ,
		[DeviceID] ,
		[OperatingSystemID],
		[OperatingSystemVersionID],
		[GeographyID] ,
		[Quantity_ThisYear],
		[Quantity_LastYear]  , 
		CONVERT(int,CONVERT(binary(2),'0x' + SUBSTRING(md5,15,4),1)) % POWER(2,14) AS HLL_HashBucket ,

		CHARINDEX('1',
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,19,1),1)) / 8) % 2 AS CHAR(1)) + 
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,19,1),1)) / 4) % 2 AS CHAR(1)) +
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,19,1),1)) / 2) % 2 AS CHAR(1)) +
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,19,1),1)) / 1) % 2 AS CHAR(1)) +

		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,20,1),1)) / 8) % 2 AS CHAR(1)) + 
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,20,1),1)) / 4) % 2 AS CHAR(1)) +
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,20,1),1)) / 2) % 2 AS CHAR(1)) +
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,20,1),1)) / 1) % 2 AS CHAR(1)) +
	
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,21,1),1)) / 8) % 2 AS CHAR(1)) + 
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,21,1),1)) / 4) % 2 AS CHAR(1)) +
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,21,1),1)) / 2) % 2 AS CHAR(1)) +
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,21,1),1)) / 1) % 2 AS CHAR(1)) +
	
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,22,1),1)) / 8) % 2 AS CHAR(1)) + 
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,22,1),1)) / 4) % 2 AS CHAR(1)) +
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,22,1),1)) / 2) % 2 AS CHAR(1)) +
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,22,1),1)) / 1) % 2 AS CHAR(1)) +
		
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,23,1),1)) / 8) % 2 AS CHAR(1)) + 
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,23,1),1)) / 4) % 2 AS CHAR(1)) +
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,23,1),1)) / 2) % 2 AS CHAR(1)) +
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,23,1),1)) / 1) % 2 AS CHAR(1)) +
		
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,24,1),1)) / 8) % 2 AS CHAR(1)) + 
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,24,1),1)) / 4) % 2 AS CHAR(1)) +
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,24,1),1)) / 2) % 2 AS CHAR(1)) +
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,24,1),1)) / 1) % 2 AS CHAR(1)) +
		
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,25,1),1)) / 8) % 2 AS CHAR(1)) + 
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,25,1),1)) / 4) % 2 AS CHAR(1)) +
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,25,1),1)) / 2) % 2 AS CHAR(1)) +
		CAST((CONVERT(int,CONVERT(binary(1),'0x0' + SUBSTRING(md5,25,1),1)) / 1) % 2 AS CHAR(1)) +
	
		'') -1 AS HLL_FirstZero 

	FROM(
	   SELECT  
		* ,  
		CONVERT(VARCHAR(50),HASHBYTES('MD5',cast(UserID as VarChar)),1) as MD5 
		FROM [fact].[myEvents]
		) AS A
```

Once the two HLL helper columns get added to the fact table, the remaining elements of the DIY HLL algorithm can get implemented in DAX by adding a calculated measure to the model

Consider the following data model along with a requirement to add an Approximate Distinct Count calculation over the UserID column in the MyEvents table.

The underlying dataset has over 50 billion rows in the MyEvents table and nearly 200 million rows in the User dimension. The requirement is to track and analyse the approximate number of active users by day or month – *and* have the ability to slice/dice by Device and/or operating system.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/12/B6.png?resize=1000%2C586&ssl=1)

To get this working, I add two HLL helper columns to the MyEvents table in the data source. In this case, the data source is an Azure SQL DW environment, so I used the TSQL shown earlier in the article.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/12/B7.png?resize=960%2C317&ssl=1)

Two HLL helper columns shown as on the right-hand side.

Then a DAX calculation similar to the following can be added to the model. I have tried to align my variable names with the logic from Phase 0 and 2 of the pseudo logic shown on page 8 from [this paper](http://static.googleusercontent.com/media/research.google.com/en/us/pubs/archive/40671.pdf). Phase 1 from the article primarily deals with the logic solve through the addition of the two pre-computed columns.

```dax
Approx Distinct Users =
	VAR p = 14  -- Number of bits for bucket
	
	VAR m = POWER ( 2, p )
	    
	VAR Registers = VALUES ( 'MyEvents'[HLL_HashBucket] )
	
	VAR CountOfRegisters = COUNTROWS(  Registers )  
	
	VAR E =
	    ( 0.7213 / ( 1 + ( 1.079 / m ) ) )               
	    * POWER ( m, 2 )
	        * POWER (
	            SUMX (
	                Registers,
	                VAR myPower =
	                    CALCULATE ( MAX ( 'MyEvents'[HLL_FirstZero] ) ) + 1
	                RETURN
	                    POWER ( 2, - myPower )
	            ),
	            -1
	        )
		
	RETURN E

```

Once the measure gets added to the model, it can be sliced and diced in visuals to produce an approximate distinct count that is reliable to within +/- 2% so long as a result is higher than 100,000. If the output may be lower than this, additional code can be added (or substituted) to either replace with [LinearCounting](http://dblab.kaist.ac.kr/Prof/pdf/ACM90_TODS_v15n2.pdf) and/or to apply a Bias correction discussed in the same paper.

## Test Results

I tested two aspects of the HLL algorithm. Testing for query speed and result accuracy. Ideally, we want this to perform much faster than DISTINCTCOUNT and still produce a value close to the actual figure.

The test environment I used is:

- Azure Windows VM. Standard B20ms (20 vcpus, **80 GiB memory**)
- Visual Studio 2017 using Analysis Services Projects installed locally
- 50 Billion-row dataset using two years of telemetry
- The data model is shown above
- 1 fully populated Visual Studio Analysis Services Tabular project without HLL helper columns
- 1 fully populated Visual Studio Analysis Services Tabular project with HLL helper columns + HLL measure

An excel file with detailed results can be [downloaded here](https://1drv.ms/x/s!AtDlC2rep7a-yGLd-z73QAHREB6h?e=pZrNQW).

To establish a baseline, I ran the following queries three times on the model without the two HLL columns. Then for the HLL testing, the five queries DAX expression was modified from using the DISTINCTCOUNT function, to call the [Approx Distinct Users] measure from above and run against an updated model with HLL. Otherwise, groupings and filtering directives were the same between the baseline and test results.

Once the queries were updated and pointed at the enhanced model, each query gets run again three times.

#### Query 1

```dax
// QUERY 1 - Just group by year.
EVALUATE 
	SUMMARIZECOLUMNS(
		// GROUP BY 
		'Date'[FirstDateofYear] ,

		// COLUMNS
		"Distinct Count" , DISTINCTCOUNT(MyEvents[UserID])
	)

```

#### Query 2

```dax
// QUERY 2 - Group by months of 2019
EVALUATE 
	SUMMARIZECOLUMNS(
		// GROUP BY 
		'Date'[FirstDateofMonth] ,

		// FILTER BY 
		TREATAS({DATE(2019,1,1)},'Date'[FirstDateofYear]) ,

		// COLUMNS
		"Distinct Count" , DISTINCTCOUNT(MyEvents[UserID])
	)

```

#### Query 3

```dax
// QUERY 3 - Group by days of June, 2019
EVALUATE 
	SUMMARIZECOLUMNS(
		// GROUP BY 
		'Date'[DateKey] ,

		// FILTER BY 
		TREATAS({DATE(2019,6,1)},'Date'[FirstDateOfMonth]) ,

		// COLUMNS
		"Distinct Count" , DISTINCTCOUNT(MyEvents[UserID])
	)

```

#### Query 4

```dax
// QUERY 4 - Group by days of June, 2019, filtered by OS
EVALUATE 
	SUMMARIZECOLUMNS(
		// GROUP BY 
		'Date'[DateKey] ,
				
		// FILTER BY 
		TREATAS({DATE(2019,6,1)},'Date'[FirstDateOfMonth]) ,
		TREATAS({"Android"},OperatingSystem[OperatingSystemName]) ,

		// COLUMNS
		"Distinct Count" , DISTINCTCOUNT(MyEvents[UserID])
	)

```

#### Query 5

```dax
//QUERY 5 - Grouped by Date, OS and Device for June 2019
EVALUATE 
	SUMMARIZECOLUMNS(
		// GROUP BY 
		'Date'[DateKey] ,
		OperatingSystem[OperatingSystemName],
		Device[DeviceName],
		
		// FILTER BY 
		TREATAS({DATE(2019,6,1)},'Date'[FirstDateOfMonth]) ,

		// COLUMNS
		"Distinct Count" , DISTINCTCOUNT(MyEvents[UserID])
	)

```

### Query 1 Results

The [first query](#query1) groups data by calendar year. This query produced three rows, with all results being within +/- 1% with the bottom two rows being within 0.4% 🙂

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/12/B8.png?resize=650%2C96&ssl=1)

Accuracy results of Query 1

The following table shows the query timing comparison between the standard DISTINCTCOUNT function and the HLL version. The top three rows show the baseline metrics, while the bottom three rows show the timing using the HLL measure.

All timings values are milliseconds and show all three HLL runs completed in under 7 seconds, compared with 171 seconds for the baseline. All other key metrics are significantly faster.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/12/B9.png?resize=471%2C159&ssl=1)

Baseline versus HLL timings in milliseconds for Query 1

### Query 2 Results

[Query 2](#query2) produces a row for each month in 2019, and again, all 9 values in the **HLL Approx Distinct Count** column were within +/- 1%, so still pretty good in terms of accuracy.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/12/B10.png?resize=641%2C192&ssl=1)

Accuracy results of Query 2

In terms of query speed, the HLL version completed in just over 10 seconds in each run, compared with over 2 minutes for each baseline run.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/12/B11.png?resize=486%2C173&ssl=1)

Baseline versus HLL timings in milliseconds for Query 2

### Query 3 Results

[Query 3](#query3) drills down to a lower grain, producing a lower output. Here we see all results are still within +/- 2%, with the most significant variation being -1.75% for June 17.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/12/B12.png?resize=657%2C606&ssl=1)

Accuracy results of Query 3

For Query 3, the HLL version was around 7 seconds compared with just under a minute for the baseline version.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/12/B13.png?resize=486%2C170&ssl=1)

Baseline versus HLL timings in milliseconds for Query 3

### Query 4 Results

[Query 4](#query4) drills down with an additional filter on Operating System Name and now generates values around the 1 million mark. Here we wee the first time a result exceeds +/- 2% on the 7th of June. All other values are OK.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/12/B14.png?resize=659%2C605&ssl=1)

Accuracy results of Query 4

Query 4 produced the fastest timings for both the baseline and the HLL versions, with the HLL version coming in around 3x faster.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/12/B15.png?resize=486%2C170&ssl=1)

Baseline versus HLL timings in milliseconds for Query 4

### Query 5 Results

The [final query](#query5) produces some interesting results in terms of accuracy. The extra degree of grouping/drill down produces small values for the first time – and the basic HLL algorithm does not provide useful estimations in these cases.

Query 5 generates around 650 rows, with some values being incredibly small. For these values, the error percentage blows out significantly past the +/- 2% as shown on the chart below. The Y-axis is logarithmic, and you can see when the DISTINCTCOUNT value was below 20,000, the error rate quickly skyrocketed.

In extreme cases, where the baseline produces a value of 1, the HLL code might generate an estimate of around 387 million. The reason for this is because we only have a single value in a single hash-bucket. We used 14 bits for our precision, so we have 16,383 empty buckets in cases like this.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/12/B19.png?resize=1000%2C427&ssl=1)

A sample of the accuracy results for query five sorted by the Distinct Count column in ascending order.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/12/B16.png?resize=1000%2C838&ssl=1)

Accuracy results of Query 5

Fortunately, the error rate for values lower than 20,000 can get solved by using [LinearCounting](http://dblab.kaist.ac.kr/Prof/pdf/ACM90_TODS_v15n2.pdf) when the HASH buckets are sparsely populated. I will cover [LinearCounting](http://dblab.kaist.ac.kr/Prof/pdf/ACM90_TODS_v15n2.pdf) in a blog soon. The [LinearCounting](http://dblab.kaist.ac.kr/Prof/pdf/ACM90_TODS_v15n2.pdf) algorithm can make use of the same HLL helper columns but counts the number of empty buckets to quickly arrive at an accurate estimate when there are a small number of unique values in large datasets.

In terms of query speed, the HLL results for query were slightly better than 2x performance, so still better, but in queries like this, we will also need to factor in the overhead of adding [LinearCounting](http://dblab.kaist.ac.kr/Prof/pdf/ACM90_TODS_v15n2.pdf) into the code.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/12/B17.png?resize=486%2C169&ssl=1)

Baseline versus HLL timings in milliseconds for Query 4

## Summary

So, in summary – the approach I describe here provides an interesting alternative for large models with slow-running distinct count queries. The technique is reasonably easy to apply, and as always, you should perform before/after benchmark testing to help determine if you should ultimately use it.

The larger your dataset, the better the algorithm performs, especially when the number of unique values grow.

Potential upsides are not just faster query speed, but a considerable reduction in resource consumption on the server hosting the model. This benefit gets amplified in multi-user scenarios. When testing, I noticed the model would consume Gigabytes of memory when running the DISTINCTCOUNT function – in single-user mode. The HLL version didn’t increase memory at all. Of course, the HLL version uses more base memory for the model, but you don’t get exposed to peaks x users per query which could saturate the server.

I also like the fact the algorithm can [be tuned](#tuning) in multiple ways so if your initial HLL test results aren’t impressive, have a look to see what tweaks can get made. Additional tuning such as VertiPaq column encoding, segment size may also provide favourable results.

The approach can apply to other data modelling environments. An MDX version of the DAX calculation could get used to generate something similar in an SSAS-MD model using the same HLL helper columns.

The main downside of the technique is the increase in the overall model footprint through the storage of the HLL helper columns. The image below shows how much additional memory the two HLL helper columns added to my test model.

![](https://i1.wp.com/dax.tips/wp-content/uploads/2019/12/B20-1.png?fit=1000%2C387&ssl=1)

You also need to manage the potential of the HLL version used to produce small values. I will cover enhancements to the DAX calculation in a future blog (Linear Counting and Bias management).

## Reference Articles

- <http://dblab.kaist.ac.kr/Publication/pdf/ACM90_TODS_v15n2.pdf>
- <https://www.rose-hulman.edu/~holden/Preprints/jha-paper.pdf>
- <https://www.dummies.com/programming/big-data/find-number-elements-data-stream/>
- <https://engineering.fb.com/data-infrastructure/hyperloglog/>
- <https://research.neustar.biz/2013/01/24/hyperloglog-googles-take-on-engineering-hll/>
- <https://odino.org/my-favorite-data-structure-hyperloglog/>
- <http://dblab.kaist.ac.kr/Prof/pdf/ACM90_TODS_v15n2.pdf>
- <https://www.cs.princeton.edu/~rs/talks/AC11-Cardinality.pdf>
- <http://algo.inria.fr/flajolet/Publications/FlFuGaMe07.pdf>
- <https://www.cms.waikato.ac.nz/~abifet/book/chapter_4.html>
- <https://docs.google.com/document/d/1gyjfMHy43U9OWBXxfaeG-3MjGzejW1dlpyMwEYAAWEI/view?fullscreen>
- <https://en.wikipedia.org/wiki/HyperLogLog>

5
2
votes

Article Rating
