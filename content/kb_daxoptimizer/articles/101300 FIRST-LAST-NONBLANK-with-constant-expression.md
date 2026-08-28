---
title: "(FIRST/LAST)NONBLANK with constant expression"
kb_id: "101300"
url: "https://kb.daxoptimizer.com/d/101300"
last_update: "May 06, 2026"
source: "DAX Optimizer Knowledge Base"
重要度:
难度:
---

# (FIRST/LAST)NONBLANK with constant expression

The function [[FIRSTNONBLANK]] or [[LASTNONBLANK]] uses a constant as expression argument.

## Remarks

The “NONBLANK” functions are all iterators that evaluate an expression for each row of the table iterated, just to check whether the result is blank or not. If the expression is a constant value, the iteration is not necessary as all the rows will always get the same value as a result, and usually it is a non-blank result if the expression is not blank.

In this condition, it is more efficient to use [[MIN]] and [[MAX]] instead of [[FIRSTNONBLANK]] and [[LASTNONBLANK]], respectively.

**Note**: [[FIRSTNONBLANK|MIN]] and [[FIRSTNONBLANK|MAX]] supported only numeric columns in early DAX versions. At that time, [[FIRSTNONBLANK]] and [[LASTNONBLANK]] were workarounds to get [[FIRSTNONBLANK|MIN]] and [[FIRSTNONBLANK|MAX]] on text columns. This is no longer required.

## Example 1

Replace [[LASTNONBLANK]] with [[MAX]] and remove the constant expression in the second argument.

### Original code

```dax
LASTNONBLANK ( Sales[Order Number], 1 )
```

### Possible optimization

```dax
MAX ( Sales[Order Number] )
```

## Example 2

Replace [[FIRSTNONBLANK]] with [[MIN]] and remove the constant expression in the second argument.

### Original code

```dax
FIRSTNONBLANK ( Sales[Order Number], 1 )
```

### Possible optimization

```dax
MIN ( Sales[Order Number] )
```
