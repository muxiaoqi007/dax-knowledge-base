---
title: "BITLSHIFT"
function: "bitlshift"
url: "https://dax.guide/bitlshift/"
source: "dax.guide"
重要度:
难度:
---

# BITLSHIFT DAX Function

Returns a number shifted left by the specified number of bits.

## Syntax

BITLSHIFT ( <Number>, <ShiftAmount> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number |  | Any expression that represents an integer. |
| ShiftAmount |  | Any expression that represents an integer as the number of bits to be shifted. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

The integer number of the first argument shifted left by the number of bits in the second argument.

## Remarks

This function can be used as a way to multiply an integer number by a power of 2.  
Be sure to understand the nature of bitshift operations and overflow/underflow of integers before using DAX bitshift functions.  
If Shift\_Amount is negative, it will shift in the opposite direction.  
In practice, the following expressions return the same result:

```dax


BITLSHIFT ( A, B )

BITRSHIFT ( A, -B )

```

If the absolute value of Shift\_Amount is larger than 64, there will be no error but overflow/underflow.  
There is no limit on Number, but the result may overflow/underflow.

## Examples

```dax


DEFINE

    VAR Vals = GENERATESERIES ( -5, 5 )

EVALUATE

    ADDCOLUMNS (

        Vals,

        "BITLSHIFT 1", BITLSHIFT ( [Value], 1 ),

        "BITLSHIFT 2", BITLSHIFT ( [Value], 2 ),

        "BITLSHIFT 3", BITLSHIFT ( [Value], 3 ),

        "BITRSHIFT 1", BITRSHIFT ( [Value], 1 ),

        "BITRSHIFT 2", BITRSHIFT ( [Value], 2 ),

        "BITRSHIFT 3", BITRSHIFT ( [Value], 3 )

    )

```

| Value | BITLSHIFT 1 | BITLSHIFT 2 | BITLSHIFT 3 | BITRSHIFT 1 | BITRSHIFT 2 | BITRSHIFT 3 |
| --- | --- | --- | --- | --- | --- | --- |
| -5 | -10 | -20 | -40 | -3 | -2 | -1 |
| -4 | -8 | -16 | -32 | -2 | -1 | -1 |
| -3 | -6 | -12 | -24 | -2 | -1 | -1 |
| -2 | -4 | -8 | -16 | -1 | -1 | -1 |
| -1 | -2 | -4 | -8 | -1 | -1 | -1 |
| 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 1 | 2 | 4 | 8 | 0 | 0 | 0 |
| 2 | 4 | 8 | 16 | 1 | 0 | 0 |
| 3 | 6 | 12 | 24 | 1 | 0 | 0 |
| 4 | 8 | 16 | 32 | 2 | 1 | 0 |
| 5 | 10 | 20 | 40 | 2 | 1 | 0 |

```dax


// This longer code produces an easier to read output 

// by showing the result in both decimal and binary formats

//

// Contribution by Kenneth Barber

DEFINE

    VAR MinimumNumber = -5

    VAR MaximumNumber = 5

    VAR MinimumShiftAmount = -3

    VAR MaximumShiftAmount = 3

    TABLE 'Numbers' =

        VAR NumberOfBitsToShow =

            //This excludes the sign bit before the "…"

            ROUNDUP (

                LOG ( MAX ( ABS ( MinimumNumber ), ABS ( MaximumNumber ) ) + 1, 2 )

                    + MAX ( ABS ( MinimumShiftAmount ), ABS ( MaximumShiftAmount ) ),

                0

            )

        RETURN

            ADDCOLUMNS (

                VAR Limit =

                    BITLSHIFT ( 1, NumberOfBitsToShow )

                RETURN

                    GENERATESERIES ( - Limit, Limit - 1 ),

                //We precompute the binary representations of all possible values

                //so that we can simply look them up later

                "Value (Binary)",

                    VAR Number = [Value]

                    RETURN

                        INT ( Number < 0 ) & "…"

                            & CONCATENATEX (

                                ADDCOLUMNS (

                                    GENERATESERIES ( 0, NumberOfBitsToShow ),

                                    "Bit", MOD ( BITRSHIFT ( Number, [Value] ), 2 )

                                ),

                                [Bit],

                                "",

                                [Value], DESC

                            )

            )



EVALUATE

GENERATE (

    CROSSJOIN (

        SELECTCOLUMNS (

            CALCULATETABLE (

                'Numbers',

                'Numbers'[Value] >= MinimumNumber,

                'Numbers'[Value] <= MaximumNumber

            ),

            "Number", [Value],

            "Number (Binary)", [Value (Binary)]

        ),

        SELECTCOLUMNS (

            GENERATESERIES ( MinimumShiftAmount, MaximumShiftAmount ),

            "Shift Amount", [Value]

        )

    ),

    VAR BITLSHIFTresult =

        BITLSHIFT ( [Number], [Shift Amount] )

    VAR BITRSHIFTresult =

        BITRSHIFT ( [Number], [Shift Amount] )

    RETURN

        ROW (

            "BITLSHIFT", BITLSHIFTresult,

            "BITLSHIFT (Binary)", LOOKUPVALUE ( 'Numbers'[Value (Binary)], 'Numbers'[Value], BITLSHIFTresult ),

            "BITRSHIFT", BITRSHIFTresult,

            "BITRSHIFT (Binary)", LOOKUPVALUE ( 'Numbers'[Value (Binary)], 'Numbers'[Value], BITRSHIFTresult )

        )

)

ORDER BY

    [Number] DESC,

    [Shift Amount] DESC

```

| Number | Number (Binary) | Shift Amount | BITLSHIFT | BITLSHIFT (Binary) | BITRSHIFT | BITRSHIFT (Binary) |
| --- | --- | --- | --- | --- | --- | --- |
| 5 | 0…0000101 | 3 | 40 | 0…0101000 | 0 | 0…0000000 |
| 5 | 0…0000101 | 2 | 20 | 0…0010100 | 1 | 0…0000001 |
| 5 | 0…0000101 | 1 | 10 | 0…0001010 | 2 | 0…0000010 |
| 5 | 0…0000101 | 0 | 5 | 0…0000101 | 5 | 0…0000101 |
| 5 | 0…0000101 | -1 | 2 | 0…0000010 | 10 | 0…0001010 |
| 5 | 0…0000101 | -2 | 1 | 0…0000001 | 20 | 0…0010100 |
| 5 | 0…0000101 | -3 | 0 | 0…0000000 | 40 | 0…0101000 |
| 4 | 0…0000100 | 3 | 32 | 0…0100000 | 0 | 0…0000000 |
| 4 | 0…0000100 | 2 | 16 | 0…0010000 | 1 | 0…0000001 |
| 4 | 0…0000100 | 1 | 8 | 0…0001000 | 2 | 0…0000010 |
| 4 | 0…0000100 | 0 | 4 | 0…0000100 | 4 | 0…0000100 |
| 4 | 0…0000100 | -1 | 2 | 0…0000010 | 8 | 0…0001000 |
| 4 | 0…0000100 | -2 | 1 | 0…0000001 | 16 | 0…0010000 |
| 4 | 0…0000100 | -3 | 0 | 0…0000000 | 32 | 0…0100000 |
| 3 | 0…0000011 | 3 | 24 | 0…0011000 | 0 | 0…0000000 |
| 3 | 0…0000011 | 2 | 12 | 0…0001100 | 0 | 0…0000000 |
| 3 | 0…0000011 | 1 | 6 | 0…0000110 | 1 | 0…0000001 |
| 3 | 0…0000011 | 0 | 3 | 0…0000011 | 3 | 0…0000011 |
| 3 | 0…0000011 | -1 | 1 | 0…0000001 | 6 | 0…0000110 |
| 3 | 0…0000011 | -2 | 0 | 0…0000000 | 12 | 0…0001100 |
| 3 | 0…0000011 | -3 | 0 | 0…0000000 | 24 | 0…0011000 |
| 2 | 0…0000010 | 3 | 16 | 0…0010000 | 0 | 0…0000000 |
| 2 | 0…0000010 | 2 | 8 | 0…0001000 | 0 | 0…0000000 |
| 2 | 0…0000010 | 1 | 4 | 0…0000100 | 1 | 0…0000001 |
| 2 | 0…0000010 | 0 | 2 | 0…0000010 | 2 | 0…0000010 |
| 2 | 0…0000010 | -1 | 1 | 0…0000001 | 4 | 0…0000100 |
| 2 | 0…0000010 | -2 | 0 | 0…0000000 | 8 | 0…0001000 |
| 2 | 0…0000010 | -3 | 0 | 0…0000000 | 16 | 0…0010000 |
| 1 | 0…0000001 | 3 | 8 | 0…0001000 | 0 | 0…0000000 |
| 1 | 0…0000001 | 2 | 4 | 0…0000100 | 0 | 0…0000000 |
| 1 | 0…0000001 | 1 | 2 | 0…0000010 | 0 | 0…0000000 |
| 1 | 0…0000001 | 0 | 1 | 0…0000001 | 1 | 0…0000001 |
| 1 | 0…0000001 | -1 | 0 | 0…0000000 | 2 | 0…0000010 |
| 1 | 0…0000001 | -2 | 0 | 0…0000000 | 4 | 0…0000100 |
| 1 | 0…0000001 | -3 | 0 | 0…0000000 | 8 | 0…0001000 |
| 0 | 0…0000000 | 3 | 0 | 0…0000000 | 0 | 0…0000000 |
| 0 | 0…0000000 | 2 | 0 | 0…0000000 | 0 | 0…0000000 |
| 0 | 0…0000000 | 1 | 0 | 0…0000000 | 0 | 0…0000000 |
| 0 | 0…0000000 | 0 | 0 | 0…0000000 | 0 | 0…0000000 |
| 0 | 0…0000000 | -1 | 0 | 0…0000000 | 0 | 0…0000000 |
| 0 | 0…0000000 | -2 | 0 | 0…0000000 | 0 | 0…0000000 |
| 0 | 0…0000000 | -3 | 0 | 0…0000000 | 0 | 0…0000000 |
| -1 | 1…1111111 | 3 | -8 | 1…1111000 | -1 | 1…1111111 |
| -1 | 1…1111111 | 2 | -4 | 1…1111100 | -1 | 1…1111111 |
| -1 | 1…1111111 | 1 | -2 | 1…1111110 | -1 | 1…1111111 |
| -1 | 1…1111111 | 0 | -1 | 1…1111111 | -1 | 1…1111111 |
| -1 | 1…1111111 | -1 | -1 | 1…1111111 | -2 | 1…1111110 |
| -1 | 1…1111111 | -2 | -1 | 1…1111111 | -4 | 1…1111100 |
| -1 | 1…1111111 | -3 | -1 | 1…1111111 | -8 | 1…1111000 |
| -2 | 1…1111110 | 3 | -16 | 1…1110000 | -1 | 1…1111111 |
| -2 | 1…1111110 | 2 | -8 | 1…1111000 | -1 | 1…1111111 |
| -2 | 1…1111110 | 1 | -4 | 1…1111100 | -1 | 1…1111111 |
| -2 | 1…1111110 | 0 | -2 | 1…1111110 | -2 | 1…1111110 |
| -2 | 1…1111110 | -1 | -1 | 1…1111111 | -4 | 1…1111100 |
| -2 | 1…1111110 | -2 | -1 | 1…1111111 | -8 | 1…1111000 |
| -2 | 1…1111110 | -3 | -1 | 1…1111111 | -16 | 1…1110000 |
| -3 | 1…1111101 | 3 | -24 | 1…1101000 | -1 | 1…1111111 |
| -3 | 1…1111101 | 2 | -12 | 1…1110100 | -1 | 1…1111111 |
| -3 | 1…1111101 | 1 | -6 | 1…1111010 | -2 | 1…1111110 |
| -3 | 1…1111101 | 0 | -3 | 1…1111101 | -3 | 1…1111101 |
| -3 | 1…1111101 | -1 | -2 | 1…1111110 | -6 | 1…1111010 |
| -3 | 1…1111101 | -2 | -1 | 1…1111111 | -12 | 1…1110100 |
| -3 | 1…1111101 | -3 | -1 | 1…1111111 | -24 | 1…1101000 |
| -4 | 1…1111100 | 3 | -32 | 1…1100000 | -1 | 1…1111111 |
| -4 | 1…1111100 | 2 | -16 | 1…1110000 | -1 | 1…1111111 |
| -4 | 1…1111100 | 1 | -8 | 1…1111000 | -2 | 1…1111110 |
| -4 | 1…1111100 | 0 | -4 | 1…1111100 | -4 | 1…1111100 |
| -4 | 1…1111100 | -1 | -2 | 1…1111110 | -8 | 1…1111000 |
| -4 | 1…1111100 | -2 | -1 | 1…1111111 | -16 | 1…1110000 |
| -4 | 1…1111100 | -3 | -1 | 1…1111111 | -32 | 1…1100000 |
| -5 | 1…1111011 | 3 | -40 | 1…1011000 | -1 | 1…1111111 |
| -5 | 1…1111011 | 2 | -20 | 1…1101100 | -2 | 1…1111110 |
| -5 | 1…1111011 | 1 | -10 | 1…1110110 | -3 | 1…1111101 |
| -5 | 1…1111011 | 0 | -5 | 1…1111011 | -5 | 1…1111011 |
| -5 | 1…1111011 | -1 | -3 | 1…1111101 | -10 | 1…1110110 |
| -5 | 1…1111011 | -2 | -2 | 1…1111110 | -20 | 1…1101100 |
| -5 | 1…1111011 | -3 | -1 | 1…1111111 | -40 | 1…1011000 |

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/bitlshift-function-dax](https://docs.microsoft.com/en-us/dax/bitlshift-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
