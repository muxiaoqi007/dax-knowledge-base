---
title: "TopNSkip Alternative"
url: "https://dax.tips/2019/05/14/topnskip-alternative"
published: "2019-05-14"
updated: "2019-07-07"
source: "dax.tips"
---

I received an email recently asking for help with an interesting DAX question. The person was trying to get a DAX query working using the undocumented **TOPNSKIP** function to return a table of data that didn’t include the first N rows.

The [[TOPNSKIP|function syntax]] is as follows :

`TOPNSKIP ( <Rows>, <Skip>, <Table> [, <OrderByExpression> [, <Order>] ] )`

To help test this, I generated a physical table inside Power BI Desktop using the following expression :

`Physical Table = GENERATESERIES( 1 , 1000 )`

This calculated table gives me a single column table with 1000 rows which I can use to test the function.

The function works as expected when I try the following:

`Test1 = TOPNSKIP(5 , 3 , 'Physical Table')`

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/05/2019-05-14_14-33-53.png?resize=596%2C307&ssl=1)

Output of TOPNSKIP function over physical table

The intellisense doesn’t recognise the TOPNFUNCTION function, but does return the expected results with 5 rows from the ‘Physical Table’ are returned starting at row 4.

However, if you try using the function with a <table expression> instead of a physical table, the following error gets generated.

`Table Test 2 = TOPNSKIP(5 , 3 , GENERATESERIES(1,1000) )`

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/05/2019-05-14_14-42-06.png?resize=833%2C121&ssl=1)

*TOPNSKIP is not supported with the given parameter combination. Please review MSDN documentation.*

This error is not a particularly useful error given the function is not documented in [MSDN](https://docs.microsoft.com/en-us/dax/topnskip-function-dax) in the first place.

Replacing the second parameter with a 0 gets rid of the error; however, this probably only works because it is syntax sugar for the TOPN function.

To get this working, I found the alternative approach :

`Table Test 3 =`   
 `FILTER(`  
 `GENERATESERIES(1,100),`  
 `ISONORAFTER([Value],4,ASC) && ISONORAFTER([Value],8,DESC)`    
 `)`

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/05/2019-05-14_14-53-23.png?resize=825%2C360&ssl=1)

The trick here is the **ISONORAFTER** can be used multiple times to set boundaries for the query. The downside is that you have to have some knowledge of the data in the column to determine the best values for the second parameter of each function.

This approach can be parameterised using variables. The datatype of the variable needs to match the column used to determine the ordering.

5
3
votes

Article Rating
