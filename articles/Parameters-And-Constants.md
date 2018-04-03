---
title:   Parameters And constants
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   09/20/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   NA
---

# Parameters And constants

## ParameterTensor{}

Creates a scalar, vector, matrix, or tensor of learnable parameters.

    ParameterTensor {shape,
                     init='uniform'/*|heNormal|...*/, initOutputRank=1, initValueScale=1.0,
                     randomSeed=-1,
                     initValue=0.0, initFromFilePath='',
                     learningRateMultiplier=1.0}

### Parameters

* `shape`: shape (dimensions) of parameter as an array. E.g. `(13:42)` to create a matrix with 13 rows and 42 columns. For some operations, dimensions given as 0 are automatically inferred [(see here)](#automatic-dimension-inference)
* `init` (default 'uniform'): specifies random initialization, e.g. `init='heNormal'` [(see here)](#random-initialization)
* `initOutputRank` (default 1): specifies number of leading fan-out axes. If negative, -number of trailing fan-out axes [(see here)](#fan-in-and-fan-out-for-random-initialization)
* `initValueScale` (default 1): additional scaling factor applied to random initialization values
* `randomSeed` (default -1): if positive, use this random seed for random initialization. If negative, use a counter that gets increased for each `ParameterTensor{}`
* `initValue`: specifies initialization with a constant value, e.g. `initValue=0`
* `initFromFilePath`: specifies initialization by loading initial values from a file. E.g. `initFromFilePath="my_init_vals.txt"`
* `learningRateMultiplier`: system learning rate will be scaled by this (0 to disable learning) [(see here)](#parameter-specific-learning-rate)

### Return Value

A tensor of learnable parameters.

### Description

This factory function creates a scalar, vector, matrix or tensor of learnable parameters, that is,
a tensor that is recognized by the `"train"` action as containing parameters
that shall be updated during training.

The values will be initialized, depending on which optional parameter is given, to
* random numbers, if `init` is given;
* a constant if `initValue` is given; or
* a tensor read from an external input file if `initFromFilePath` is given.
The default is `init="uniform"`.

To create a scalar, vector, matrix, or tensor with rank>2, pass the following as the `shape` parameter:
* `(1)` for a scalar;
* `(M)` for a column vector with `M` elements;
* `(1:N)` for a row vector with `N` elements. Row vectors are one-row matrices;
* `(M:N)` for a matrix with `N` rows and `I` columns;
* `(I:J:K...)` for a tensor of arbitrary rank>2 (note: the maximum allowed rank is 12); and
* `(W:H:C)` for a tensor that matches the dimensions of an `[W x H]` image with `C` channels.

#### Automatic dimension inference
When a `ParameterTensor` is used for weights as an immediate input of specific operations, it is allowed to specify
some dimensions as `Inferred`. For example, the [matrix product](./Times-and-TransposeTimes.md) `ParameterTensor{42:Inferred} * x)`
will automatically infer the second dimension to be equal to the dimension of `x`.

This is extremely handy for inputs of layers,
as it frees the user's BrainScript code from the burden of
passing around the input dimensions.
Further, in some situations it is very cumbersome to determine the precise input dimensions of a layer,
for example for the first fully connected layer on top of a pyramid of convolution/pooling combinations
without padding, where each convolution and pooling operation may drop rows or columns of boundary
pixels, and strides scale the dimensions.

This feature is what allows CNTK's [predefined layers](./BrainScript-Layers-Reference.md) to be specified by their output dimension only
(e.g. [`DenseLayer`](./BrainScript-Layers-Reference.md#denselayer-linearlayer)`{1024}`).

#### Random initialization
Random initialization is selected by the `init` parameter,
which chooses between uniform and normal distribution,
where the range/standard deviation is computed as a function of fan-in and fan-out:

| value of `init` | distribution  | range/standard deviation  |
| --------------- |:-------------:| ------------------------- |
| 'heNormal'      | normal        | sqrt (2 / fanIn)          |
| 'heUniform'     | uniform       | sqrt (6 / fanIn)          |
| 'glorotNormal'  | normal        | sqrt (2 / (fanIn+fanOut)) |
| 'glorotUniform' | uniform       | sqrt (6 / (fanIn+fanOut)) |
| 'xavier'        | uniform       | sqrt (3 / fanIn)          |
| 'uniform'       | uniform       | 1/20                      |
| 'gaussian'      | normal        | sqrt (0.04 / fanIn)       |
| 'zero'          | n/a           | 0                         |

(Where `zero` is a sometimes convenient alternative to specifying `initValue=0`.) For uniform distribution, the parameters will be initialized uniformly in [-range, range]; for normal distribution, the mean is always zero. 

Note that the default for `init` is `uniform` when using `ParameterTensor{}` directly.
However, default is `glorotUniform` for [layers](./BrainScript-Layers-Reference.md) that contain parameters inside, such as [`DenseLayer{}`](./BrainScript-Layers-Reference.md#denselayer-linearlayer) and [`ConvolutionalLayer{}`](./BrainScript-Layers-Reference.md#convolutionallayer).

#### Fan-in and fan-out for random initialization
Random initialization assumes that the parameters are part of some form of matrix-product like operation
which has a well-defined fan-in and fan-out, which are used in determining the scaling of the random values
per above table.
By default, the first axis is considered fan-out, and the remaining axis/axes are fan-in, matching
semantics of the regular matrix product.

The optional parameter `initOutputRank` can be used to specify the number of leading axes that
should be considered fan-out.
For example, for a matrix product in [CNTK's extended tensor interpretation](./Times-and-TransposeTimes.md#extended-interpretation-of-matrix-product-for-tensors-of-rank--2) that maps a `[K]`-dimensional vector `x`
to a `[I x J]`-dimensional rank-2 object can be written as [`Times`](./Times-and-TransposeTimes.md)` (W, x, outputRank=2)`,
where `W` has the shape `[I x J x K]`.
Here, `initOutputRank=2` specifies that in scaling the random initialization values,
the fan-out is `I*J` and the fan-in `K`.

Negative values for `initOutputRank` indicate that the fan-out axes are trailing axes. For example,
the filter kernel of the [`ConvolutionalLayer{}`](./BrainScript-Layers-Reference.md#convolutionallayer) and the underlying [`Convolution()`](./Convolution.md)  operation
for a typical image-processing setup
has a shape `[W x H x C x K]`, where `K` is the fan-out, while the fan-in is `W*H*C`.
This is specified by `initOutputRank=-1`.

#### Reading initial values from files
The initial values can be read from a text file. To do this, pass a pathname for the optional
parameter `initFromFilePath`.
The text file is expected to consist of one line per matrix rows, which consist of space-separated numbers, one per column.
The row and column dimensions in the file must match `shape`.

#### Parameter-specific learning rate
Parameter-specific learning rates can be realized with the optional `learningRateMultiplier` parameter.
This factor is multiplied with the actual learning rate when performing parameter updates.
For example, if specified as 0, the parameter will not be updated, it is constant.

### Examples

A regular parameter matrix that will be initialized as `heUniform` (default would be `heNormal`):

    W = ParameterTensor {(outDim:inDim), init=`heUniform`}

A regular bias parameter that will be initialized as zero:

    b = ParameterTensor {outDim, initValue=0}

An embedding matrix that should be read from a file and kept constant:

    E = ParameterTensor {(embedDim:vocabSize),
                         initFromFilePath='./EmbeddingMatrix.txt',
                         learningRateMultiplier=0}  # prevents learning

A bias parameter of the full size of an `[width x height]`-size image with `numChannels` color planes:

    bFull = ParameterTensor {(width:height:numChannels)}

## Constant{}

Create a constant tensor.

    Constant {scalarValue, rows=1, cols=1}

### Parameters

* `scalarValue`: value of this constant
* `rows` (default: 1): number of rows, if constant is not a scalar
* `cols` (default: 1): number of cols, if constant is not a scalar

### Return Value

A constant, either a scalar or a rank-1 or rank-2 object of dimension `[rows x cols]`, where all elements are
filled with `scalarValue`.

### Description

A constant value.
It can be either a scalar, or a rank-1 object (vector) or rank-2 object (matrix) initialized with a single value (such as 0).
Note that because for vector and matrix constants all values are identical,
constants used in conjunction with [element-wise operations](./Binary-Operations.md) can often be specified as a scalar,
while  taking advantage of [broadcasting](./Binary-Operations.md#broadcasting-semantics).

### Implementation note:

A `Constant()` is a `ParameterTensor{}` with `learningRateMultiplier=0`.

### Examples

Interpolation between two values with interpolation weight `alpha` in range 0..1 ("soft multiplexer"):

    SoftMUX (x, y, alpha) = Constant (1-alpha) .* x + Constant (alpha) .* y

Hamming loss (cf. [here](./How-do-I-Train-Models-in-BrainScript.md#train-a-multilabel-classifier)):

    HammingLoss (y, p) = ReduceSum (BS.Boolean.Xor (y, Greater (p, Constant(0.5))))
    hl = HammingLoss (multiLabels, probabilities)
