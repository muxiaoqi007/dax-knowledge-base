---
title: "Function replace IFERROR/DIVIDE"
kb_id: "101000"
url: "https://kb.daxoptimizer.com/d/101000"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# Function replace IFERROR/DIVIDE

The [[IFERROR]] function can be replaced by [[DIVIDE]].

## Remarks

The [[IFERROR]] function catches any error, including conversion errors. When the expression is a [division operator](https://dax.guide/op/division/), the error that is more likely to happen is a division by zero. The [[DIVIDE]] function specifically manages the division by zero condition without intercepting any other error, resulting in faster execution time.

The actual performance impact could be smaller than the value shown, which is the maximum in the worst-case scenario. However, the use of IFERROR would hide any error, not just a division by zero: therefore, when [[IFERROR]] was used just to remove a division-by-zero error, it is always a good idea to use [[DIVIDE]].

## DirectQuery

You can [**Ignore**](https://docs.daxoptimizer.com/glossary/ignored) the issue if the model is in DirectQuery mode.

This optimization can be counterproductive in [DirectQuery models](https://docs.daxoptimizer.com/reference/directquery-models). The [[IFERROR]] function is usually folded into a query to the underlying database, whereas the [[DIVIDE]] function is not. Therefore, this optimization should be used only when the model is in Import mode.

## Example

Replace the [[IFERROR]] function with [[DIVIDE]], moving the denominator expression in the second argument of [[DIVIDE]]. The second argument of [[IFERROR]] becomes the third argument of [[DIVIDE]].

### Original code

```dax
IFERROR (
    [Amount] / [Quantity], 
    0
)
```

### Possible optimization

```dax
DIVIDE (
    [Amount],
    [Quantity],
    0
)
```
