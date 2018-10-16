---
title:   Reduction operations
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   02/10/2017
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   NA
---

# Reduction operations

Reduce an input, e.g. compute sum or mean over elements.

    ReduceSum (x, axis=None)
    ReduceLogSum (x, axis=None)
    ReduceMean (x, axis=None)
    ReduceMax (x, axis=None)
    ReduceMin (x, axis=None)

## Parameters

* `x`: data to reduce
* `axis` (default: `None`): if specified, perform reduction along this axis only. This value is 1-based; i.e. 1 stands for the first static axis of `x`.

## Return value

Reduced value. For `axis=1` (default), this is a scalar. If an axis is specified, that axis
is reduced to have dimension 1.

## Description

These functions compute aggregates (sum, mean, etc.) over all values of an input vector or tensor.
Available aggregations are:
* `ReduceSum()`: the sum over the elements
* `ReduceLogSum()`: the sum over elements in log representations (`logC = log (exp (logA) + exp (logB))`)
* `ReduceMean()`: the mean over the elements
* `ReduceMax()`: the maximum value of the elements
* `ReduceMin()`: the minimum value

By default, aggregation is done over all elements.
In case of a tensor with rank>1, the optional `axis` parameter specifies a single axis
that the reduction is performed over.
For example, `axis=2` applied to a `[M x N]`-dimensional matrix would aggregate over all columns,
yielding a `[M x 1]` result.

### Reducing over sequences
If the input is a sequence, reduction is performed separately for every sequence item.
These operations do not support reduction over sequences.
Instead, you can achieve this with a recurrence.
For example, to sum up all elements of a sequence `x`, you can say:

    sum = x + PastValue (0, sum, defaultHiddenActivation=0)

and for max pooling, you can use

    max = Max(x, PastValue (0, max, defaultHiddenActivation=0))

## Examples

Normalize a value by subtracting the mean of its elements (e.g. as part of [layer normalization](./BrainScript-Layers-Reference.md#batchnormalizationlayer-layernormalizationlayer-stabilizerlayer)):

    mean = ReduceMean (x)
    xNorm = x - mean

Or, the [cross-entropy with softmax](./Loss-Functions-and-Metrics.md#crossentropy-crossentropywithsoftmax) criterion can be
manually be defined using `ReduceLogSum()`:

    myCrossEntropyWithSoftmax (y/*label*/, z/*logit*/) = ReduceLogSum (z) - ReduceSum (y .* z)
