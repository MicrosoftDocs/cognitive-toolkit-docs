---
title:   Binary Operations
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   08/26/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# Binary Operations


Elementwise binary operators.

    ElementTimes (x, y)
    x .* y
    Minus (x, y)
    x - y
    Plus (x, y)
    x + y
    LogPlus (x, y)
    Less (x, y)
    Equal (x, y)
    Greater (x, y)
    GreaterEqual (x, y)
    NotEqual (x, y)
    LessEqual (x, y)
    BS.Boolean.And (x, y)
    BS.Boolean.Or (x, y)
    BS.Boolean.Xor (x, y)

## Parameters

* `x`: left input
* `y`: right input

The dimensions of `x` and `y` must match (subject to [broadcasting rules](#broadcasting-semantics), see below).

For the three `Boolean` operations, both inputs are expected to be either 0 or 1, otherwise the behavior of
the functions is unspecified, and will in fact change in future versions.

Sparse values are currently not supported.

## Return value

These functions return the result of the corresponding operations. The relation operators (`Equal()` etc.)
and the three `Boolean` operations return values that
are either 0 or 1.

The output dimension or tensor shape is identical to those of the inputs, subject to broadcasting, see below.

## Descriptions

These are the common binary operators.
They are applied elementwise.
(Note that BrainScript's `*` operator is *not* elementwise, but stands for the [matrix product](./Times-and-TransposeTimes.md). This is different, for example, from Python's `numpy` library.)

The dimensions of the inputs must be identical, with the exception of broadcasting.

### Broadcasting semantics

Broadcasting, a concept that CNTK models after Python's [`numpy`](http://www.numpy.org/) library,
means that a dimension in one of the inputs can be 1 where the other input's is not.
In that case, the input with the 1-dimension will be copied `n` times, where `n` is the
corresponding other input's dimension.
If the tensor ranks do not match, the tensor shape of the input with less dimensions will be
assumed to be 1, and trigger broadcasting.

For example, adding a `[13 x 1]` tensor to a `[1 x 42]` vector would yield a `[13 x 42]` vector
that contains the sums of all combinations.

### Relation Operations
The relation operators (`Equal()` etc.) are not differentiable, their gradient is always considered 0.
They can be used for flags, e.g. as a condition argument in the [`If()`](./If-Operation.md) operation.

### LogPlus()
The `LogPlus()` operation computes the sum of values represented in logarithmic form.
I.e., it computes:

    LogPlus (x, y) = Log (Exp (x) + Exp (y))

where `x` and `y` are logarithms of values.
This operation is useful when dealing with probabilities,
which are often so small that only a logarithmic representation
allows for appropriate numeric accuracy.

Note: Another common name for this operation is log-add-exp, e.g. [SciPy](http://docs.scipy.org/doc/numpy/reference/generated/numpy.logaddexp.html).

## Examples

### Standard Sigmoid Layer
This layer uses the elementwise binary `+`:

    z = Sigmoid (W * x + b)

Note that `*` above is *not* elementwise, but stands for the [matrix product](./Times-and-TransposeTimes.md).

### Alternative Implementation of Softmax Activation
The [`Softmax()`](./BrainScript-Activation-Functions.md) activation function can be written using broadcasting `Minus`:

    MySoftmax (z) = Exp (z - ReduceLogSum (z))

Here, `ReduceLogSum()` reduces the vector `z` to a scalar by computing its logarithmic sum. Through broadcasting semantics
of subtraction, this scalar is then subtracted from every input value.
This implements the division by the sum over all values in the Softmax function.

### Elementwise Max of Two Inputs
The elementwise maximum of two inputs can be computed as a combination of `Greater()` and [`If()`](./If-Operation.md):

    MyElementwiseMax (a, b) = If (Greater (a, b), a, b)

This also works with broadcasting. For example, the linear rectifier can be written with this using
a scalar constant as the second input:

    MyReLU (x) = MyElementwiseMax (x, Constant(0))
