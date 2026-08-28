---
title: "BITXOR"
function: "bitxor"
category: "Logical"
url: "https://dax.guide/bitxor/"
source: "dax.guide"
重要度:
难度:
---

# BITXOR DAX Function (Logical)

Returns a bitwise ‘XOR’ of two numbers.

## Syntax

BITXOR ( <Number1>, <Number2> )

| Parameter | Attributes | Description |
| --- | --- | --- |
| Number1 |  | The first number. |
| Number2 |  | The second number. |

## Return values

Scalar A single [integer](https://dax.guide/dt/integer/) value.

A bitwise XOR of two numbers.

## Examples

```dax


DEFINE

    VAR ValA = SELECTCOLUMNS ( GENERATESERIES ( -5, 5 ), "A", [Value] )

    VAR ValB = SELECTCOLUMNS ( GENERATESERIES ( -5, 5 ), "B", [Value] )

EVALUATE

    ADDCOLUMNS (

        CROSSJOIN ( ValA, ValB ),

        "BITAND", BITAND ( [A], [B] ),

        "BITOR", BITOR ( [A], [B] ),

        "BITXOR", BITXOR ( [A], [B] )

    )

ORDER BY [A] DESC, [B] DESC

```

| A | B | BITAND | BITOR | BITXOR |
| --- | --- | --- | --- | --- |
| 5 | 5 | 5 | 5 | 0 |
| 5 | 4 | 4 | 5 | 1 |
| 5 | 3 | 1 | 7 | 6 |
| 5 | 2 | 0 | 7 | 7 |
| 5 | 1 | 1 | 5 | 4 |
| 5 | 0 | 0 | 5 | 5 |
| 5 | -1 | 5 | -1 | -6 |
| 5 | -2 | 4 | -1 | -5 |
| 5 | -3 | 5 | -3 | -8 |
| 5 | -4 | 4 | -3 | -7 |
| 5 | -5 | 1 | -1 | -2 |
| 4 | 5 | 4 | 5 | 1 |
| 4 | 4 | 4 | 4 | 0 |
| 4 | 3 | 0 | 7 | 7 |
| 4 | 2 | 0 | 6 | 6 |
| 4 | 1 | 0 | 5 | 5 |
| 4 | 0 | 0 | 4 | 4 |
| 4 | -1 | 4 | -1 | -5 |
| 4 | -2 | 4 | -2 | -6 |
| 4 | -3 | 4 | -3 | -7 |
| 4 | -4 | 4 | -4 | -8 |
| 4 | -5 | 0 | -1 | -1 |
| 3 | 5 | 1 | 7 | 6 |
| 3 | 4 | 0 | 7 | 7 |
| 3 | 3 | 3 | 3 | 0 |
| 3 | 2 | 2 | 3 | 1 |
| 3 | 1 | 1 | 3 | 2 |
| 3 | 0 | 0 | 3 | 3 |
| 3 | -1 | 3 | -1 | -4 |
| 3 | -2 | 2 | -1 | -3 |
| 3 | -3 | 1 | -1 | -2 |
| 3 | -4 | 0 | -1 | -1 |
| 3 | -5 | 3 | -5 | -8 |
| 2 | 5 | 0 | 7 | 7 |
| 2 | 4 | 0 | 6 | 6 |
| 2 | 3 | 2 | 3 | 1 |
| 2 | 2 | 2 | 2 | 0 |
| 2 | 1 | 0 | 3 | 3 |
| 2 | 0 | 0 | 2 | 2 |
| 2 | -1 | 2 | -1 | -3 |
| 2 | -2 | 2 | -2 | -4 |
| 2 | -3 | 0 | -1 | -1 |
| 2 | -4 | 0 | -2 | -2 |
| 2 | -5 | 2 | -5 | -7 |
| 1 | 5 | 1 | 5 | 4 |
| 1 | 4 | 0 | 5 | 5 |
| 1 | 3 | 1 | 3 | 2 |
| 1 | 2 | 0 | 3 | 3 |
| 1 | 1 | 1 | 1 | 0 |
| 1 | 0 | 0 | 1 | 1 |
| 1 | -1 | 1 | -1 | -2 |
| 1 | -2 | 0 | -1 | -1 |
| 1 | -3 | 1 | -3 | -4 |
| 1 | -4 | 0 | -3 | -3 |
| 1 | -5 | 1 | -5 | -6 |
| 0 | 5 | 0 | 5 | 5 |
| 0 | 4 | 0 | 4 | 4 |
| 0 | 3 | 0 | 3 | 3 |
| 0 | 2 | 0 | 2 | 2 |
| 0 | 1 | 0 | 1 | 1 |
| 0 | 0 | 0 | 0 | 0 |
| 0 | -1 | 0 | -1 | -1 |
| 0 | -2 | 0 | -2 | -2 |
| 0 | -3 | 0 | -3 | -3 |
| 0 | -4 | 0 | -4 | -4 |
| 0 | -5 | 0 | -5 | -5 |
| -1 | 5 | 5 | -1 | -6 |
| -1 | 4 | 4 | -1 | -5 |
| -1 | 3 | 3 | -1 | -4 |
| -1 | 2 | 2 | -1 | -3 |
| -1 | 1 | 1 | -1 | -2 |
| -1 | 0 | 0 | -1 | -1 |
| -1 | -1 | -1 | -1 | 0 |
| -1 | -2 | -2 | -1 | 1 |
| -1 | -3 | -3 | -1 | 2 |
| -1 | -4 | -4 | -1 | 3 |
| -1 | -5 | -5 | -1 | 4 |
| -2 | 5 | 4 | -1 | -5 |
| -2 | 4 | 4 | -2 | -6 |
| -2 | 3 | 2 | -1 | -3 |
| -2 | 2 | 2 | -2 | -4 |
| -2 | 1 | 0 | -1 | -1 |
| -2 | 0 | 0 | -2 | -2 |
| -2 | -1 | -2 | -1 | 1 |
| -2 | -2 | -2 | -2 | 0 |
| -2 | -3 | -4 | -1 | 3 |
| -2 | -4 | -4 | -2 | 2 |
| -2 | -5 | -6 | -1 | 5 |
| -3 | 5 | 5 | -3 | -8 |
| -3 | 4 | 4 | -3 | -7 |
| -3 | 3 | 1 | -1 | -2 |
| -3 | 2 | 0 | -1 | -1 |
| -3 | 1 | 1 | -3 | -4 |
| -3 | 0 | 0 | -3 | -3 |
| -3 | -1 | -3 | -1 | 2 |
| -3 | -2 | -4 | -1 | 3 |
| -3 | -3 | -3 | -3 | 0 |
| -3 | -4 | -4 | -3 | 1 |
| -3 | -5 | -7 | -1 | 6 |
| -4 | 5 | 4 | -3 | -7 |
| -4 | 4 | 4 | -4 | -8 |
| -4 | 3 | 0 | -1 | -1 |
| -4 | 2 | 0 | -2 | -2 |
| -4 | 1 | 0 | -3 | -3 |
| -4 | 0 | 0 | -4 | -4 |
| -4 | -1 | -4 | -1 | 3 |
| -4 | -2 | -4 | -2 | 2 |
| -4 | -3 | -4 | -3 | 1 |
| -4 | -4 | -4 | -4 | 0 |
| -4 | -5 | -8 | -1 | 7 |
| -5 | 5 | 1 | -1 | -2 |
| -5 | 4 | 0 | -1 | -1 |
| -5 | 3 | 3 | -5 | -8 |
| -5 | 2 | 2 | -5 | -7 |
| -5 | 1 | 1 | -5 | -6 |
| -5 | 0 | 0 | -5 | -5 |
| -5 | -1 | -5 | -1 | 4 |
| -5 | -2 | -6 | -1 | 5 |
| -5 | -3 | -7 | -1 | 6 |
| -5 | -4 | -8 | -1 | 7 |
| -5 | -5 | -5 | -5 | 0 |

```dax


// This longer code produces an easier to read output 

// by showing the result in both decimal and binary formats

//

// The second query also shows how to implement a BITNOT function,

// which is not available in DAX

//

// Contribution by Kenneth Barber

DEFINE

    VAR MinimumValue = -3

    VAR MaximumValue = 3

    TABLE 'Numbers' =

        VAR NumberOfBitsToShow =

            // This excludes the sign bit before the "…"

            ROUNDUP (

                LOG ( MAX ( ABS ( MinimumValue ), ABS ( MaximumValue ) ) + 1, 2 ),

                0

            )

        RETURN

            ADDCOLUMNS (

                VAR Limit =

                    BITLSHIFT ( 1, NumberOfBitsToShow )

                RETURN

                    GENERATESERIES ( - Limit, Limit - 1 ),

                // We precompute the binary representations of all possible values

                // so that we can simply look them up later

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

    MEASURE 'Numbers'[Binary] =

        // Where this measure is used, we could have used LOOKUPVALUE instead,

        // but it would have been more verbose

        CALCULATE (

            VALUES ( 'Numbers'[Value (Binary)] ),

            ALLEXCEPT ( 'Numbers', 'Numbers'[Value] )

        )

    VAR TableOfValuesToShow =

        CALCULATETABLE (

            'Numbers',

            'Numbers'[Value] >= MinimumValue,

            'Numbers'[Value] <= MaximumValue

        )

    VAR TableA =

        SELECTCOLUMNS (

            TableOfValuesToShow,

            "A", [Value],

            "A (Binary)", [Value (Binary)]

        )

    VAR TableB =

        SELECTCOLUMNS (

            TableOfValuesToShow,

            "B", [Value],

            "B (Binary)", [Value (Binary)]

        )



EVALUATE

GENERATE (

    CROSSJOIN ( TableA, TableB ),

    VAR BITANDresult =

        BITAND ( [A], [B] )

    VAR BITORresult =

        BITOR ( [A], [B] )

    VAR BITXORresult =

        BITXOR ( [A], [B] )

    RETURN

        ROW (

            "BITAND", BITANDresult,

            "BITAND (Binary)", CALCULATE ( [Binary], 'Numbers'[Value] = BITANDresult ),

            "BITOR", BITORresult,

            "BITOR (Binary)", CALCULATE ( [Binary], 'Numbers'[Value] = BITORresult ),

            "BITXOR", BITXORresult,

            "BITXOR (Binary)", CALCULATE ( [Binary], 'Numbers'[Value] = BITXORresult )

        )

)

ORDER BY

    [A] DESC,

    [B] DESC



// There is no "BITNOT" function in DAX to perform a bitwise NOT,

// but BITXOR(,-1) achieves the same result (-1 = 1…1111)

EVALUATE

GENERATE (

    SELECTCOLUMNS (

        // This ensures that the column name is not preceded with the table name

        TableOfValuesToShow,

        "Value", [Value],

        "Value (Binary)", [Value (Binary)]

    ),

    VAR BITNOTresult =

        BITXOR ( [Value], -1 )

    RETURN

        ROW (

            """BITNOT""", BITNOTresult,

            """BITNOT"" (Binary)", CALCULATE ( [Binary], 'Numbers'[Value] = BITNOTresult )

        )

)

ORDER BY [Value] DESC



```

| A | A (Binary) | B | B (Binary) | BITAND | BITAND (Binary) | BITOR | BITOR (Binary) | BITXOR | BITXOR (Binary) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 3 | 0…011 | 3 | 0…011 | 3 | 0…011 | 3 | 0…011 | 0 | 0…000 |
| 3 | 0…011 | 2 | 0…010 | 2 | 0…010 | 3 | 0…011 | 1 | 0…001 |
| 3 | 0…011 | 1 | 0…001 | 1 | 0…001 | 3 | 0…011 | 2 | 0…010 |
| 3 | 0…011 | 0 | 0…000 | 0 | 0…000 | 3 | 0…011 | 3 | 0…011 |
| 3 | 0…011 | -1 | 1…111 | 3 | 0…011 | -1 | 1…111 | -4 | 1…100 |
| 3 | 0…011 | -2 | 1…110 | 2 | 0…010 | -1 | 1…111 | -3 | 1…101 |
| 3 | 0…011 | -3 | 1…101 | 1 | 0…001 | -1 | 1…111 | -2 | 1…110 |
| 2 | 0…010 | 3 | 0…011 | 2 | 0…010 | 3 | 0…011 | 1 | 0…001 |
| 2 | 0…010 | 2 | 0…010 | 2 | 0…010 | 2 | 0…010 | 0 | 0…000 |
| 2 | 0…010 | 1 | 0…001 | 0 | 0…000 | 3 | 0…011 | 3 | 0…011 |
| 2 | 0…010 | 0 | 0…000 | 0 | 0…000 | 2 | 0…010 | 2 | 0…010 |
| 2 | 0…010 | -1 | 1…111 | 2 | 0…010 | -1 | 1…111 | -3 | 1…101 |
| 2 | 0…010 | -2 | 1…110 | 2 | 0…010 | -2 | 1…110 | -4 | 1…100 |
| 2 | 0…010 | -3 | 1…101 | 0 | 0…000 | -1 | 1…111 | -1 | 1…111 |
| 1 | 0…001 | 3 | 0…011 | 1 | 0…001 | 3 | 0…011 | 2 | 0…010 |
| 1 | 0…001 | 2 | 0…010 | 0 | 0…000 | 3 | 0…011 | 3 | 0…011 |
| 1 | 0…001 | 1 | 0…001 | 1 | 0…001 | 1 | 0…001 | 0 | 0…000 |
| 1 | 0…001 | 0 | 0…000 | 0 | 0…000 | 1 | 0…001 | 1 | 0…001 |
| 1 | 0…001 | -1 | 1…111 | 1 | 0…001 | -1 | 1…111 | -2 | 1…110 |
| 1 | 0…001 | -2 | 1…110 | 0 | 0…000 | -1 | 1…111 | -1 | 1…111 |
| 1 | 0…001 | -3 | 1…101 | 1 | 0…001 | -3 | 1…101 | -4 | 1…100 |
| 0 | 0…000 | 3 | 0…011 | 0 | 0…000 | 3 | 0…011 | 3 | 0…011 |
| 0 | 0…000 | 2 | 0…010 | 0 | 0…000 | 2 | 0…010 | 2 | 0…010 |
| 0 | 0…000 | 1 | 0…001 | 0 | 0…000 | 1 | 0…001 | 1 | 0…001 |
| 0 | 0…000 | 0 | 0…000 | 0 | 0…000 | 0 | 0…000 | 0 | 0…000 |
| 0 | 0…000 | -1 | 1…111 | 0 | 0…000 | -1 | 1…111 | -1 | 1…111 |
| 0 | 0…000 | -2 | 1…110 | 0 | 0…000 | -2 | 1…110 | -2 | 1…110 |
| 0 | 0…000 | -3 | 1…101 | 0 | 0…000 | -3 | 1…101 | -3 | 1…101 |
| -1 | 1…111 | 3 | 0…011 | 3 | 0…011 | -1 | 1…111 | -4 | 1…100 |
| -1 | 1…111 | 2 | 0…010 | 2 | 0…010 | -1 | 1…111 | -3 | 1…101 |
| -1 | 1…111 | 1 | 0…001 | 1 | 0…001 | -1 | 1…111 | -2 | 1…110 |
| -1 | 1…111 | 0 | 0…000 | 0 | 0…000 | -1 | 1…111 | -1 | 1…111 |
| -1 | 1…111 | -1 | 1…111 | -1 | 1…111 | -1 | 1…111 | 0 | 0…000 |
| -1 | 1…111 | -2 | 1…110 | -2 | 1…110 | -1 | 1…111 | 1 | 0…001 |
| -1 | 1…111 | -3 | 1…101 | -3 | 1…101 | -1 | 1…111 | 2 | 0…010 |
| -2 | 1…110 | 3 | 0…011 | 2 | 0…010 | -1 | 1…111 | -3 | 1…101 |
| -2 | 1…110 | 2 | 0…010 | 2 | 0…010 | -2 | 1…110 | -4 | 1…100 |
| -2 | 1…110 | 1 | 0…001 | 0 | 0…000 | -1 | 1…111 | -1 | 1…111 |
| -2 | 1…110 | 0 | 0…000 | 0 | 0…000 | -2 | 1…110 | -2 | 1…110 |
| -2 | 1…110 | -1 | 1…111 | -2 | 1…110 | -1 | 1…111 | 1 | 0…001 |
| -2 | 1…110 | -2 | 1…110 | -2 | 1…110 | -2 | 1…110 | 0 | 0…000 |
| -2 | 1…110 | -3 | 1…101 | -4 | 1…100 | -1 | 1…111 | 3 | 0…011 |
| -3 | 1…101 | 3 | 0…011 | 1 | 0…001 | -1 | 1…111 | -2 | 1…110 |
| -3 | 1…101 | 2 | 0…010 | 0 | 0…000 | -1 | 1…111 | -1 | 1…111 |
| -3 | 1…101 | 1 | 0…001 | 1 | 0…001 | -3 | 1…101 | -4 | 1…100 |
| -3 | 1…101 | 0 | 0…000 | 0 | 0…000 | -3 | 1…101 | -3 | 1…101 |
| -3 | 1…101 | -1 | 1…111 | -3 | 1…101 | -1 | 1…111 | 2 | 0…010 |
| -3 | 1…101 | -2 | 1…110 | -4 | 1…100 | -1 | 1…111 | 3 | 0…011 |
| -3 | 1…101 | -3 | 1…101 | -3 | 1…101 | -3 | 1…101 | 0 | 0…000 |

| Value | Value (Binary) | “BITNOT” | “BITNOT” (Binary) |
| --- | --- | --- | --- |
| 3 | 0…011 | -4 | 1…100 |
| 2 | 0…010 | -3 | 1…101 |
| 1 | 0…001 | -2 | 1…110 |
| 0 | 0…000 | -1 | 1…111 |
| -1 | 1…111 | 0 | 0…000 |
| -2 | 1…110 | 1 | 0…001 |
| -3 | 1…101 | 2 | 0…010 |

Last update: Aug 1, 2026   [» Contribute](# "Contribute to DAX Guide by submitting suggestions to improve the content and by reporting any issue.")   [» Show contributors](#)

Contributors: Alberto Ferrari, Marco Russo, Kenneth Barber,

Microsoft documentation: [https://docs.microsoft.com/en-us/dax/bitxor-function-dax](https://docs.microsoft.com/en-us/dax/bitxor-function-dax?WT.mc_id=DP-MVP-4025372)

2018-2026 © SQLBI. All rights are reserved. Information coming from Microsoft documentation is property of Microsoft Corp. [» Contact us](mailto:info@sqlbi.com?subject=DAX%20Guide)   [» Privacy Policy & Cookies](https://dax.guide/privacy/)
