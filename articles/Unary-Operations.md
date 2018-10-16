---
Title:   Unary operations
Author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   08/15/2016
ms.prod: cntk
ms.topic:   conceptual
ms.devlang:   NA
---

# Unary operations

Common unary elementwise functions and operations.

    Abs (x)
    Ceil (x)
    Cosine (x)
    Clip (x, minValue, maxValue)
    Exp (x)
    Floor (x)
    Log (x)
    Negate (x)
    -x
    BS.Boolean.Not (b)
    !b
    Reciprocal (x)
    Round (x)
    Sin (x)
    Sqrt (x)

## Parameters

* `x`: argument to apply the function or operation to

Sparse values are currently not supported.

`Clip():`

* `minValue`: inputs less than this value are replaced by this value
* `maxValue`: likewise, inputs more than this value are replaced by this value

## Return Value

Result of applying the function or operation. The output's tensor shape is the same as the input's.

## Description

These are common functions and unary operations.

Note that `BS.Boolean.Not()` expects inputs to be 0 or 1.

## Example

    MySoftmax (z) = Exp (LogSoftmax (z))
