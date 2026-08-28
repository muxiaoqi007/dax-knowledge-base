---
title: "DAX: Bitwise calculations"
url: "https://dax.tips/2019/10/03/dax-bitwise-calculations"
published: "2019-10-02"
updated: "2019-10-03"
source: "dax.tips"
---

As an extension to my recent article on [[dax-base-conversions|base conversions]] in DAX, I thought I’d show how these conversion calculations can be used to help you perform bitwise calculations in DAX. At present, there are no bitwise operators in DAX (I’m working on it!!), so calculations that need to perform [AND](#and), [OR](#or), [XOR](#xor), [NOT](#not) etc. can’t be done with a single operator.

What am I talking about?

Algorithms and formulae sometimes require two numbers to be processed using a [bitwise math](https://en.wikipedia.org/wiki/Bitwise_operation) operation. The idea is to take two numbers like 7 and 4, and produce an output that is the result of applying some logic over the underlying bits. The operation needs to take place at the binary level.

In the figure below, the output of a logical AND over values 7and 4 produces a result of 4 is shown in the top batch. Both 7 and 4 are first converted to a binary representation which is then vertically aligned. A logical AND is applied for every column that produces a result in the bottom row showing either a 1 or 0. The middle and bottom batches show the workings and output when using logical OR and XOR.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/2019-10-03_9-00-35.png?resize=406%2C357&ssl=1)

Once the vertical tests get applied, a new binary number can be converted back to base 10 to show the correct output.

In languages other than DAX, an operator such as the ampersand can be used between two numbers (like + or -) to perform a logical bitwise AND calculation.

|  |  |  |  |  |
| --- | --- | --- | --- | --- |
|  | AND | OR | XOR | NOT |
| [T-SQL](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/bitwise-operators-transact-sql?view=sql-server-2017) | & | | | ^ | ~ |
| [C#](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators) | & | | | ^ | ~ |
| [Javascript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators) | & | | | ^ | ~ |

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/2019-10-03_9-22-06.png?resize=808%2C427&ssl=1)

The query above shows how simple it is to perform a bit-wise calculation in T-SQL using one of the bit-wise operators. However, we do not have these operators in DAX.

### How to do it in DAX

The first step is to convert your base 10 value to a binary representation (in text). I provide a sample [[dax-base-conversions|DEC2BIN]] calculation in a previous article. Then, simply run along the length of binary text in a single iterator ([CONCATENATEX](https://docs.microsoft.com/en-us/dax/concatenatex-function-dax)), and test the values stored in each position and create a new text value that can be converted back to decimal ([[dax-base-conversions|BIN2DEC]])

The DAX snippets below are configured to work as a calculated column, but can easily be converted to work inside calculated measures.

You can [download a small PBIX file here](https://1drv.ms/u/s!AtDlC2rep7a-xj7kfLr9eMZ19Kra?e=gYFtAh) that contains some sample data and all these calculations in use in a table.

#### Bitwise AND

```dax
Bitwise AND = 
VAR BitTable = GENERATESERIES(1,LEN([Value as Binary]))
VAR Number_1 = 'Bitwise Examples'[Value as Binary]
VAR Number_2 = 'Bitwise Examples'[4 as Binary]
RETURN
    CONCATENATEX(
            BitTable,
            VAR B1 = MID(Number_1,[value],1) 
            VAR B2 = MID(Number_2,[Value],1) 
            RETURN 
                IF(B1="1" && B2="1","1","0"),
                ,
                [Value],
                ASC
                )

```

#### Bitwise OR

```dax
Bitwise OR = 
VAR BitTable = GENERATESERIES(1,LEN([Value as Binary]))
VAR Number_1 = 'Bitwise Examples'[Value as Binary]
VAR Number_2 = 'Bitwise Examples'[4 as Binary]
RETURN
    CONCATENATEX(
            BitTable,
            VAR B1 = MID(Number_1,[value],1) 
            VAR B2 = MID(Number_2,[Value],1) 
            RETURN 
                IF(B1="1" || B2="1","1","0"),
                ,
                [Value],
                ASC
                )

```

#### Bitwise XOR

```dax
Bitwise XOR = 
VAR BitTable = GENERATESERIES(1,LEN([Value as Binary]))
VAR Number_1 = 'Bitwise Examples'[Value as Binary]
VAR Number_2 = 'Bitwise Examples'[4 as Binary]
RETURN
    CONCATENATEX(
            BitTable,
            VAR B1 = MID(Number_1,[value],1) 
            VAR B2 = MID(Number_2,[Value],1) 
            RETURN 
                IF(B1=B2,"0","1"),
                ,
                [Value],
                ASC
                )

```

#### Bitwise NOT

```dax
Bitwise NOT = 
VAR BitTable = GENERATESERIES(1,LEN([Value as Binary]))
VAR Number_1 = 'Bitwise Examples'[Value as Binary]
RETURN
    CONCATENATEX(
            BitTable,
            VAR B1 = MID(Number_1,[value],1) 
            RETURN 
                IF(B1="1","0","1"),
                ,
                [Value],
                ASC
                )

```

As you see, the basic pattern is the same across the examples. Additional bitwise operations can be applied, like left-shift, rotate etc. just as easily.

Please let me know if you think exposing native bitwise operators would be useful in DAX so I can add weight to having these added to the core language.

Bitwise operations can be useful for many scenarios. They can be used for compression, encryption and high performance scenarios, particularity in native mode (not using text conversion as shown here).

I wrote a fully working [MD5 Hashing calculation](https://www.ietf.org/rfc/rfc1321.txt) in DAX using the above code, by applying the rules laid out in RFC 1321 and it was pretty good.

Earlier in my software development past, I often used these types of operations to store and retrieve multiple states from a single 32bit integer for speed and compression. It would be great to see these come to DAX. But for now, we can use techniques as outlined in this blog.

5
7
votes

Article Rating
