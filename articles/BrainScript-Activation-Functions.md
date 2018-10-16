---
title:   Activation  Functions with BrainScript
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   03/09/2017
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   brainscript
---

# Activation  Functions with BrainScript

## Sigmoid(), Tanh(), ReLU(), Softmax(), LogSoftmax(), Hardmax()

Non-linear activation functions for neural networks.

    Sigmoid (x)
    Tanh (x)
    ReLU (x)
    Softmax (x)
    LogSoftmax (x)
    Hardmax (x)

### Parameters

* `x`: argument to apply the non-linearity to

### Return Value

Result of applying the non-linearity. The output's tensor shape is the same as the input's.

### Description

These are the popular activation functions of neural networks.
All of these except the `Softmax()` family and `Hardmax()` are applied elementwise.

Note that for efficiency, when using the cross-entropy training criterion,
it is often desirable to not apply a Softmax operation at the end,
but instead pass the *input* of the Softmax to `CrossEntropyWithSoftmax()`

The `Hardmax()` operation determines the element with the highest value
and represents its location as a one-hot vector/tensor.
This is used for performing classification.

#### Expressing Other Non-Linearities in BrainScript

If your needed non-linearity is not one of the above,
it may be composable as a BrainScript expression.
For example, a leaky ReLU with a slope of 0.1 for the negative part could just be written as

    LeakyReLU (x) = 0.1 * x + 0.9 * ReLU (x)

#### Softmax Along Axes

The Softmax family is special in that it involves the computation of a denominator.
This denominator is computed over all values of the input vector.

In some scenarios, however, the input is a tensor with rank>1, where axes should be treated separately.
Consider, for example, an input tensor of shape `[10000 x 20]` that stores 20 different distributions,
each column represents the probability distribution of a distinct input item.
Hence, the Softmax operation should compute 20 separate denominators.
This operation is not supported by the built-in `(Log)Softmax()` functions, but can be realized
in BrainScript using an elementwise reduction operation as follows:

    ColumnwiseLogSoftmax (z) = z - ReduceLogSum (axis=1)

Here, [`ReduceLogSum()`](./Reduction-Operations.md) computes the (log of) the denominator, resulting in a tensor
with dimension 1 for the reduced axis; `[1 x 20]` in the above example. Subtracting this from the
`[10000 x 20]`-dimensional input vector is a valid operation--as usual, the `1` will automatically "broadcast",
that is, duplicated to match the input dimension.

### Example

A simple MLP that performs a 10-way classification of 40-dimensional feature vectors:

    features = Input{40}
    h = Sigmoid (ParameterTensor{256:0} * features + ParameterTensor{256})
    z = ParameterTensor{10:0}  * h * ParameterTensor{10}   # input to Softmax
    labels = Input{10}
    ce = CrossEntropyWithSoftmax (labels, z)

