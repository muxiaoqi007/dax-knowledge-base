---
title: "DAX: Base conversions"
url: "https://dax.tips/2019/10/02/dax-base-conversions"
published: "2019-10-02"
updated: "2019-10-16"
source: "dax.tips"
---

I was doing some work recently which required DAX calculations to convert values from a specific base to another, e.g. to convert a value in hexadecimal format to a base 10 representation (or decimal), or from base 10 to binary.

Excel, like other languages has native functions to allow you to convert across base, such as the following :

- [HEX2DEC](#hex2dec) – to convert a hexadecimal
  - FF = 255
- [DEC2BIN](#DEC2BIN) – to convert a decimal number to binary
  - 255 = 1111 1111
- [BIN2DEC](#BIN2DEC) – convert a binary number to decimal
  - 1111 = 15
- [DEC2HEX](#DEC2HEX) – convert a decimal number to hexadecimal
  - 15 = F

As part of that work, I created the following patterns that can be used to perform these types of conversions in DAX. The code snippets are pretty bare, so assumptions are made they are applied over clean data. This means there is no code to detect values like ‘X’ in a HEX2DEC conversion.

The code should be pretty straight forward to follow, and can easily be modified to work in a calculated column or measure.

Just assign the value you want to convert to the **ConvertMe** variable in each case and the final RETURN statement will convert as required.

You can create a calculated table to pre-process likely conversions required to help speed you up, and the patterns can easily be converted for other base-type conversions.

### HEX2DEC

```dax
EVALUATE 
	VAR ConvertMe = "FF" 
	VAR HEX2DEC = 
		SUMX(
			// Loop control
			GENERATESERIES(0,LEN(ConvertMe)-1) ,
			// Inner Calc
			VAR c = MID(ConvertMe,LEN(ConvertMe) - [value],1)
			VAR d = SWITCH(c,"A",10,"B",11,"C",12,"D",13,"E",14,"F",15,int(c))
			RETURN 
				d * POWER(16,[value])
			)
	RETURN 
		{ HEX2DEC } // Returns 255

```

### DEC2BIN

```dax
EVALUATE
VAR ConvertMe = 55
VAR BitTable =
    GENERATESERIES ( 1, 8 )
VAR DEC2BIN =
    CONCATENATEX (
        BitTable,
        MOD ( TRUNC ( ConvertMe / POWER ( 2, [value] - 1 ) ), 2 ),
        ,
        [Value], DESC
    ) 		
RETURN
    { DEC2BIN } //Returns 001101111

```

### BIN2DEC

```dax
EVALUATE
VAR ConvertMe = "0000101110"
VAR BIN2DEC =
    SUMX (
        // Loop control
        GENERATESERIES ( 0, LEN ( ConvertMe ) - 1 ),
        // Inner Calc
        VAR c =
            MID ( ConvertMe, LEN ( ConvertMe ) - [value], 1 )
        RETURN
            c * POWER ( 2, [value] )
    )
RETURN
    { BIN2DEC }	// Returns 46

```

### DEC2HEX

```dax
EVALUATE
VAR ConvertMe = 255
VAR Base = 16
VAR BitTable =
    GENERATESERIES ( 1, 8 )
VAR DEC2HEX = 
    CONCATENATEX( 
        BitTable,
        VAR c = MOD ( TRUNC ( ConvertMe / POWER ( base, [value] - 1 ) ),base )
        RETURN SWITCH(c,10,"A",11,"B",12,"C",13,"D",14,"E",15,"F",c),
        ,
        [Value],Desc)
     		
RETURN
    { DEC2HEX } // Returns 'FF'

```

This is probably not something you are likely to need for many reports. But it’s fun to work these out using basic maths.

I used some of these calculations in a Tic Tac Toe game I wrote in DAX last year. This time around, I was doing something much more business practical. 🙂

A PBIX file can be [downloaded here](https://1drv.ms/u/s!AtDlC2rep7a-xi9Ild_lL-t3qwHl?e=9wBl1i) with these calculations loaded into a calculated table.

![](https://i0.wp.com/dax.tips/wp-content/uploads/2019/10/2019-10-02_13-47-17.png?resize=598%2C301&ssl=1)

Once you have converted your values to Binary, you can use [[dax-bitwise-calculations|this article]] to help include bitwise operations in your calculations.

Let me know what you think 🙂

4.4
9
votes

Article Rating
