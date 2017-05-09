---
title:   Layers  Reference with BrainScript
author:    chrisbasoglu
date:    03/09/2017
ms.author:   cbasoglu
ms.date:   03/09/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# Layers  Reference with BrainScript

CNTK predefines a number of common "layers," which makes it very easy to write simple networks
that consist of standard layers layered on top of each other.
Layers are function objects that can be used like regular BrainScript functions but
hold learnable parameters and
have an additional pair of `{}` to pass construction parameters or attributes.

For example, this is the network description for a simple 1-hidden layer model using the `DenseLayer{}` layer:

    h = DenseLayer {1024, activation=ReLU} (features)
    p = DenseLayer {9000, activation=Softmax} (h)

which can then, e.g., be used for training against a cross-entropy criterion:

    ce = CrossEntropy (labels, p)

If your network is a straight concatenation of operations (many are), you can use the alternative
[`Sequential()`](./Sequential.md)
notation:

    myModel = Sequential (
        DenseLayer {1024, activation=ReLU} :
        DenseLayer {9000, activation=Softmax}
    )

and invoke it like this:

    p = myModel (features)

## Example models

The following shows a slot tagger that embeds a word sequence, processes it with a recurrent LSTM,
and then classifies each word:

    taggingModel = Sequential (
        EmbeddingLayer {150} :      # embed into a 150-dimensional vector
        RecurrentLSTMLayer {300} :  # forward LSTM
        DenseLayer {labelDim}       # word-wise classification
    )

And the following is a simple convolutional network for image recognition:

    convNet = Sequential (
        # 3 layers of convolution and dimension reduction by pooling
        ConvolutionalLayer {32, (5:5), pad=true, activation=ReLU} :
        MaxPoolingLayer {(3:3), stride=(2:2)} :
        ConvolutionalLayer {32, (5:5), pad=true, activation=ReLU} :
        MaxPoolingLayer {(3:3), stride=(2:2)} :
        ConvolutionalLayer {64, (5:5), pad=true, activation=ReLU} :
        MaxPoolingLayer {(3:3), stride=(2:2)} :
        # 2 dense layers for classification
        DenseLayer {64, activation=ReLU} :
        LinearLayer {10}
    )

## Parameter sharing
If you assign a layer to a variable and use it in multiple places,
*the parameters will be shared*.
If you say

    lay = DenseLayer {1024, activation=Sigmoid}
    h1 = lay (x)
    h2 = lay (h1)  # same weights as `h1`

`h1` and `h2` will *share the same parameters*, as `lay()` is the *same function* in both cases.
In the above case this is probably
not what was desired, so be aware.
If both invocations of `lay()` above are meant to have different parameters,
remember to define two separate instances, for example `lay1 = DenseLayer{...}` and `lay2 = DenseLayer{...}`.

So why this behavior?
Layers allow to share parameters across sections of a model.
Consider a DSSM model which processes two input images, say `doc` and `query`
identically with the same processing chain, and compares the resulting hidden vectors:

    imageToVec = Sequential (
        ConvolutionalLayer {32, (5:5), pad = true, activation = ReLU} :
        MaxPoolingLayer {(3:3), stride = (2:2)} :
        ConvolutionalLayer {64, (5:5), pad = true, activation = ReLU} :
        MaxPoolingLayer {(3:3), stride = (2:2)} :
        DenseLayer {64, activation = ReLU} :
        LinearLayer {10}
    )
    zDoc   = imageToVec (doc)
    zQuery = imageToVec (query)  # same model as for zDoc
    sim = CosDistance (zdoc, zQuery)

where `imageToVec` is the part of the model that converts images into flat vector.
`imageToVec` is a function object that in turn contains several function objects (e.g. three instances of
`ConvolutionalLayer{}`).
`imageToVec` is instantiated *once*, and this instance holds the learnable parameters of all
the included function objects. Both invocations of `model()` will share
these parameters in application, and their gradients will be the sum of both invocations.

Lastly, note that if in the above example `query` and `doc` must have the same dimensions,
since they are processed through the same function object, and that function object's first
layer has its input dimension inferred to match that of both `query` and `doc`.
If their dimensions differ,
then this network is malformed, and dimension inference/validation will fail with an error message.

## Implementation note
Many layers are wrappers around underlying CNTK primitives, along with the respective
required learnable parameters. For example,
[`ConvolutionalLayer{}`](#convolutionallayer) wraps the [`Convolution()`](./Convolution.md) primitive.
The benefits of using layers are:
* layers contain learnable parameters of the correct dimension
* layers are composable (cf. [`Sequential()`](./Sequential.md))

## DenseLayer{}, LinearLayer{}

Factory function to create a fully-connected layer. `DenseLayer{}` takes with an optional non-linearity.

    DenseLayer {outDim, activation=Identity, init='glorotUniform', initValueScale=1, bias=true}
    LinearLayer {outDim, init='glorotUniform', initValueScale=1, bias=true}

### Parameters

* `outDim`: output dimension of this layer
* `activation` (`DenseLayer{}` only): pass a function here to be used as the activation function, such as `activation=ReLU`
* `init` (`'heNormal'`|`'glorotUniform'`|...): type of initialization for the weights. [See here](./Parameters-And-Constants.md#random-initialization) for a full list of initialization options.
* `initValueScale`: the variance random initialization is multiplied with this
* `bias`: if false, do not include a bias parameter

### Return Value

A function that implements the desired fully-connected layer. See description.

### Description

Use these factory functions to create a fully-connected layer.
Use `DenseLayer{}` if you would like an activation function to be included, otherwise `LinearLayer{}`.

Each these factory functions create a function object that contains a learnable weight matrix and,
unless `bias=false`, a learnable bias. The function object can be used like a function,
which implements one of these formulas:

    DenseLayer{...} (v) = activation (W * v + b)
    LinearLayer{...} (v) = W * v + b

where `W` is a weight matrix of dimension `[outDim x (dimension of v)]`, `b` is the bias of dimension `[outdim]`,
and the resulting value has dimension (or tensor dimensions) as given by `outDim`.

#### Tensor support
If the returned function is applied to an input of a tensor rank > 1, e.g. a 2D image,
`W` will have the dimension `[outDim x (first dimension of input) x (second dimension of input) x ...]`.

On the other hand, `outDim` can be a vector that specifies tensor dimensions, for example `(10:10)`.
In that case,
`W` will have the dimension `[outDim[0] x outDim[1] x ... x (dimension of input)]`,
and `b` will have the tensor dimensions `[outDim[0] x outDim[1] x ...]`.

CNTK's matrix product will interpret these extra output or input dimensions as if they were flattened into a long vector.
For more details on this, see the documentation of [`Times()`](./Times-And-TransposeTimes.md)

### Example:

    h = DenseLayer {1024, activation=Sigmoid) (v)

or alternatively:

    Layer = DenseLayer {1024, activation=Sigmoid)
    h = Layer (v)

## ConvolutionalLayer{}

Creates a convolution layer with optional non-linearity.

    ConvolutionalLayer {numOutputChannels, filterShape,
                        activation = Identity,
                        init = 'glorotUniform', initValueScale = 1,
                        stride = 1, pad = false, lowerPad = 0, upperPad = 0,
                        bias = true}

### Parameters

* `numOutputChannels`: number of output channels (number of filters)
* `filterShape`: *spatial* extent of the filter, e.g. `(5:5)` for a 2D filter. The input-channel dimension
is *not* to be included here.
* `activation`: optional non-linearity, e.g. `activation=ReLU`
* `init` (`'heNormal'`|`'glorotUniform'`|...): type of random initialization for the weights. [See here](./Parameters-And-Constants.md#random-initialization) for a full list of random-initialization options.
* `initValueScale`: the variance random initialization is multiplied with this
* `stride`: increment when sliding the filter over the input. E.g. `(2:2)` to reduce the dimensions by 2
* `pad`: if not set (default), then the filter will be shifted over the "valid" area of input, that is, no value outside
the area is used. If `pad` is set on the other hand, the filter will be applied to all input positions,
and values outside the valid region will be considered zero.
* `lowerPad`, `upperPad`: explicitly specify different margins for padding. Filters will
be shifted over a valid region that is (virtually) augmented with zeroes. E.g. `lowerPad=(1:2)` will append a
column of zeroes and two rows of zeroes.
The dimension of the output is extended accordingly.
* `bias`: if false, do not include a bias parameter

### Return Value

A function that implements the desired fully-connected layer. See description.

### Description

Use these factory functions to create a convolution layer.

The resulting layer applies a convolution operation on an N-dimensional tensor.
The caller specifies the spatial extend of the filter.
A set of filters of a given spatial extent (e.g. `(5:5)`) is correlated with every location of the input
(e.g. a `[640 x 480]`-sized image).
Assuming padding is enabled (`pad`) and strides are 1, this will generate an output region of the same dimension
(`[640 x 480]`).

Typically, many filters are applied at the same time. `numOutputChannels` specifies the number,
so for every input location, an entire vector of `numOutputChannels` is produced.
For our example above, setting `numOutputChannels` to 64 would in a `[640 x 480 x 64]`-sized tensor.
That last axis is called the *channel dimension*.

When convolution is applied to an input with an channel dimension,
each filter will also consist of vectors of the input's channel dimension.
E.g. when applying convolution
with a specified spatial filter extent of `(5:5)` to a `[640 x 480 x 3]`-sized color image,
each filter will be a `[5 x 5 x 3]` tensor.

All `numOutputChannels` filters stacked together is called the *kernel*.
In our example, the kernel shape will be `[5 x 5 x 3 x 64]`.

The following summarizes the relationship between the various dimensions and shapes:

    input shape    : [ (spatial dims)  x  (#input channels)                        ]
    spatial extent : [ (filterShape)                                               ]
    output shape   : [ (spatial dims)  x                     x  numOutputChannels  ]
    kernel shape   : [ (filterShape)   x  (#input channels)  x  numOutputChannels  ]

which in our example are:

    input shape    : [ 640 x 480   x   3                          ]
    spatial extent : [   5 x 5                                    ]
    output shape   : [ 640 x 480   x       x   numOutputChannels  ]
    kernel shape   : [   5 x 5     x   3   x   numOutputChannels  ]

#### Padding
If padding is not enabled, then the output region will be reduced by the boundary locations to which the full
filter extent cannot be applied. E.g. applying a `(5:5)`-extent filter to an image without padding,
the outermost 2 rows and columns of pixels would cause the filter to be applied out of bounds.
Hence, `ConvolutionalLayer{}` will reduce the dimensions accordingly.

An `[640 x 480]` image convolved with a `(5:5)` filter without padding
will leave a `[636 x 476]`-sized output region.

#### Strides
The `stride` parameters specify the increment of filters.
Stride values greater than one will lead to a sub-sampling of the output region.
E.g. filtering a `[640 x 480]` image with a stride of `(2:2)` will result in a `[320 x 240]`-sized
region with padding, and `[318 x 238]` without padding.

#### Notes
This layer is a wrapper around the [`Convolution()`](./Convolution.md) primitive.

The filter kernel parameters' name as shown in the log's validation section will end in `.W`.
The dimension will currently *not* be shown as `[ (filterShape) x (#input channels) x numOutputChannels ]`
as described above, but instead [ numOutputChannels x ((product over filter shape) * (#input channels)) ]`.

### Example:

    c = ConvolutionalLayer {64, (3:3), pad = true, stride = (1:1), bias=false} (x)

## DeconvLayer{}

Creates a deconvolution layer.

    DeconvLayer {numOutputChannels,
                 filterShape, numInputChannels,
                 bias = true,
                 activation = (x=>x),
                 init = 'glorotUniform',
                 initValueScale = 0.001,
                 initBias = 0,
                 stride = 1, autoPadding = false,
                 lowerPad = 0, upperPad = 0,
                 maxTempMemSizeInSamples = 0}

### Parameters

* `numOutputChannels`: number of output channels (number of filters)
* `filterShape`: *spatial* extent of the filter, e.g. `(5:5)` for a 2D filter. The input-channel dimension
is *not* to be included here.
* `numInputChannels`: number of input channels (number of filters of the input volume)
* `bias`: if false, do not include a bias parameter
* `activation`: optional non-linearity, e.g. `activation=ReLU`
* `init` (`'heNormal'`|`'glorotUniform'`|...): type of random initialization for the weights. [See here](./Parameters-And-Constants.md#random-initialization) for a full list of random-initialization options.
* `initValueScale`: the variance random initialization is multiplied with this
* `initBias`: the initial value for the bias
* `stride`: increment when sliding the filter over the input. E.g. `(2:2)` to reduce the dimensions by 2
* `autoPadding`: if not set (default), then the filter will be shifted over the "valid" area of input, that is, no value outside the area is used. If `autoPadding` is set on the other hand, the filter will be applied to all input positions,
and values outside the valid region will be considered zero.
* `lowerPad`, `upperPad`: explicitly specify different margins for padding __for the output volume, i.e. the ones that were used for the input in the corresponding convolutional layer__. It is important to set them in correspondence with the convolutional layer to achieve the same tensor dimensions. 

### Return Value

A function that implements the desired fully-connected layer. See description.

### Description

Use these factory functions to create a deconvolution layer.

The resulting layer applies a deconvolution operation on an N-dimensional tensor.
This layer is a wrapper around the [`Convolution()`](./Convolution.md) primitive with `deconv=true`. 

One popular usecase for deconvolution is reconstructing an image ([see here for example](https://arxiv.org/abs/1505.04366)). Where convolution takes an input 2D receptive-field region and computes the correlation with a 2D filter, deconvolution takes a pixel and spreads it over a 2D region.

Consider an image p(.,.), a pixel location (x,y) and a centered [3 x 3] filter with the following content (no feature-map depth dimension for now, i.e. one single channel):

    [ . . c
      a b .
      . . . ]

Here, a b and c are weights of the filter, ‘.’ Corresponds to a zero weight. Convolution() computes the output pixel q(x, y) at location (x, y) as:

    q(x,y) = b * p(x,y) + a * p(x-1,y) + c * p(x+1,y-1)

Deconvolution takes pixels q(x,y) and spreads them over a region around (x,y). If we used the same filter, it would make the following contributions to the output r(x,y):

    r(x,y)     += b * q(x,y)
    r(x-1,y)   += a * q(x,y)
    r(x+1,y-1) += c * q(x,y) 

Knowing that the same applies for all x and y across the plane, we can express this for r(x,y):

    r(x,y)     += b * q(x,y)
    r(x,y)     += a * q(x+1,y)
    r(x,y)     += c * q(x-1,y+1) 

or in total, 

    r(x,y) = b * q(x,y) + a * q(x+1,y) + c * q(x-1,y+1) 

This has the same form as the Convolution above, except that the filter is mirrored along both axes.

Now we introduce feature maps into the mix. That is easy: Instead of going from input depth to output depth, we go the other direction.

In summary, Convolution (W, x) == Deconvolution (W’, x), where

    W : [W x H x C x K]

and

`W’ = W` with its values rearranged as: `[(W mirrored) x (H mirrored) x K x C]`

I.e. what Deconvolution() does implicitly is:

* exchange the two depth dimensions (transpose)
* the spatial dimensions (reverse the order of the data)
* Convolution() with these

### Example:

    deconv_A = DeconvLayer {inputDim, (5:5), cMap1, lowerPad=(2:2:0), upperPad=(2:2:0)}(unpool_A)

See [Image auto encoder using Deconvolution and Unpooling](./Image-auto-encoder-using-Deconvolution-and-Unpooling.md) for a detailed example and walk through.

## MaxPoolingLayer{}, AveragePoolingLayer{}

Factory functions to create a max- or average-pooling layer.

    MaxPoolingLayer {poolShape, stride = 1, pad = false, lowerPad = 0, upperPad = 0}
    AveragePoolingLayer {poolShape, stride = 1, pad = false, lowerPad = 0, upperPad = 0} =

### Parameters

* `poolShape`: the shape of the region to pool, e.g. `(2:2)`
* `stride`: increment when sliding the pool over the input. E.g. `(2:2)` to reduce the dimensions by 2
* `pad`: if not set (default), then the pool will be shifted over the "valid" area of input, that is, no value outside
the area is used. If `pad` is set on the other hand, the pool will be applied to all input positions,
and values outside the valid region will be considered zero. For average pooling, count for average does not include padded values. 
* `lowerPad`, `upperPad`: explicitly specify different margins for padding. Filters will
be shifted over a valid region that is (virtually) augmented with zeroes. E.g. `lowerPad=(1:2)` will append a
column of zeroes and two rows of zeroes.
The dimension of the output is extended accordingly.

### Return Value

A function that implements the desired pooling layer. See description.

### Description

Use this factory function to create a pooling operation. Use `MaxPoolingLayer{}` to
compute the maximum over the values in the pool area, and `AveragePoolingLayer{}` to take their average.

The pooling operation slides a "pool window" over locations of an input region,
and computes either the maximum or the average of the values in the respective pool region.

This operation is structurally very similar to convolution, except that the operation
applied to the sliding window is of a different nature.

All considerations regarding input dimensions, padding, and strides apply identically, so please
see [`ConvolutionalLayer{}`](#convolutionallayer) for more detail.

### Example:

    p = MaxPoolingLayer {(3:3), stride=(2:2)} (c)

## MaxUnoolingLayer{}

Creates a max-unooling layer.

    MaxUnpoolingLayer {poolShape,
                       stride = 1, pad = false,
                       lowerPad = 0, upperPad = 0} 

### Parameters

* `poolShape`: the shape of the region to unpool (the __output__ region size), e.g. `(2:2)`
* `stride`: increment when sliding the pool over the __output__. E.g. `(2:2)` to increase the dimensions by 2
* `pad`: if not set (default), then the pool will be shifted over the "valid" area of output, that is, no value outside the area is used.
* `lowerPad`, `upperPad`: explicitly specify different margins for padding. Filters will
assume a valid output region that is (virtually) augmented.

### Return Value

A function that implements the desired unpooling layer. See description.

### Description

Use this factory function to create an unpooling operation.

The unpooling operation is the inverse of a pooling operation. It requires two inputs: the output of its corresponding pooling layer, say `p1` and the input of its corresponding pooling layer, say `r1`, too. It slides an "inverse pool window" over locations of its input `p1`, and projects the value to that position of the output region that had the maximum value in the corresponding pooling operation, i.e. in `r1`. The second input `r1` is required in CNTK to determine the target of the Unpooling operation, since CNTK does not store so called _switch variables_ ([see here for details](https://arxiv.org/abs/1505.04366)). 

### Example:

    unpool_A = MaxUnpoolingLayer {(2:2), stride=(2:2)}(deconv_B, relu_A)

See [Image auto encoder using Deconvolution and Unpooling](./Image-auto-encoder-using-Deconvolution-and-Unpooling.md) for a detailed example and walk through.

## EmbeddingLayer{}

    EmbeddingLayer {outDim,
                    init='glorotUniform', initValueScale=1,
                    embeddingPath = '', transpose = false}

### Parameters

* `outDim`: the dimension of the desired embedding vector
* `init` (`'heNormal'`|`'glorotUniform'`|...): type of initialization for the weights. [See here](./Parameters-And-Constants.md#random-initialization) for a full list of initialization options.
* `initValueScale`: the variance random initialization is multiplied with this
* `embeddingPath`: if given, embeddings are not learned but loaded from a file and not updated further during training
* `transpose`: allows to load embeddings that are stored in transposed form

### Return Value

A function that implements the embedding layer. See description.

### Description

"Embedding" refers to representing words or other discrete items by dense continuous vectors.
This layer assumes that the input is in one-hot form. E.g., for a vocabulary size of 10,000,
each input vector is expected to have dimension 10,000 and consist of zeroes except for one
position that contains a 1. The index of that location is the index of the word or item it represents.

In CNTK, the corresponding embedding vectors are stored as columns of a matrix.
Hence, mapping an input word to its embedding is implemented as a matrix product.
For this to be very efficient, it is important that the input vectors are stored in sparse format.

Fun fact: The gradient of an embedding matrix has the form of gradient vectors that are only
non-zero for words seen in a minibatch. Since for realistic vocabularies of tens or hundreds of thousands,
the vast majority of columns would be zero,
CNTK implements has a specific optimization to represent the gradient in "column-sparse" form.

Known issue: The above-mentioned column-sparse gradient form is currently not supported by
our [1-bit SGD](./Multiple-GPUs-and-machines.md#21-data-parallel-training-with-1-bit-sgd) parallelization technique. Please use the [block-momentum](./Multiple-GPUs-and-machines.md#22-block-momentum-sgd) technique instead.

### Example

A learned embedding that represents words from a vocabulary of 87636 as a 300-dimensional vector:

    input = Input{87636, sparse=true}    # word sequence, as one-hot vector, sparse format
    embEn = EmbeddingLayer{300} (input)  # embed word as a 300-dimensional continuous vector

In addition to `sparse=true`, one should also declare an input as sparse in the `reader` config block.
Here is an example of reading sparse text input with the [`CNTKTextFormatReader`](./BrainScript-CNTKTextFormat-Reader.md):

    reader = {
        readerType = "CNTKTextFormatReader"
        file = "en2fr.txt"
        randomize = true
        input = {
            input  = { alias = "E" ; dim = 87636 ;  format = "sparse" }
            labels = { alias = "F" ; dim = 98624 ;  format = "sparse" }
        }
    }

If, instead, the embedding vectors already exist and should be loaded from a file, it would look like this:

    input = Input{87636, sparse=true}     # word sequence, as one-hot vector, sparse format
    embEn = EmbeddingLayer{300, embeddingPath="embedding-en.txt", transpose=true} (w) # embedding from disk

where the file `"embedding-en.txt"` would be expected to consist of 87,636 text rows,
each of which consisting of 300 space-separated numbers.
Since this file saves the embeddings as rows rather than columns, `transpose=true` will transpose
the matrix on the fly.

## RecurrentLSTMLayer{}, RecurrentLSTMLayerStack{}

Factory functions to create a single-layer or multi-layer recurrent LSTM.

    RecurrentLSTMLayer {outDim, cellShape = None,
                        goBackwards = false,
                        usePeepholes = false,
                        init = 'glorotUniform', initValueScale = 1,
                        enableSelfStabilization = false,
                        allowOptimizedEngine = false}
    RecurrentLSTMLayerStack {layerDims,
                             cellShapes = None,
                             usePeepholes = false,
                             init = 'glorotUniform', initValueScale = 1,
                             enableSelfStabilization = false,
                             allowOptimizedEngine = false}

### Parameters

* `outDim` (`RecurrentLSTMLayer{}`): dimension of the network's output. To denote a tensor of rank>1, this can be a vector, e.g. `(40:2)`
* `layerDims` (`RecurrentLSTMLayerStack{}`): array of dimensions of the network's inner layers and output
* `cellShape` ( (`RecurrentLSTMLayer{}`, optional): the dimension of the LSTM's cell. Normally this is identical to `outDim`. If a different value is given, an additional linear projection will be inserted to convert from the cell dimension to the output.
* `cellShapes` ( (`RecurrentLSTMLayerStack{}`, optional): array of values like `cellShape` for `RecurrentLSTMLayer()` to denote projection
* `goBackwards` (optional): if true, the recurrence is run backwards
* `usePeepholes` (optional): if true, then use peephole connections in the LSTM
* `init` (`'heNormal'`|`'glorotUniform'`|...): type of initialization for the weights. [See here](./Parameters-And-Constants.md#random-initialization) for a full list of initialization options.
* `initValueScale`: the variance random initialization is multiplied with this
* `enableSelfStabilization` (optional): if true, insert a "stabilizer" operation similar to [`StabilizerLayer{}`](#batchnormalizationlayer-layernormalizationlayer-stabilizerlayer)
* `allowOptimizedEngine` (optional, default false): if true, then use cudnn5's optimized RNN engine where possible

### Return Value

A function that implements the desired layer(s) that applies/apply a recurrent LSTM to its input sequence.
This layer (-stack) maps an input sequence to a sequence of hidden states of the same length.

### Description

This implements the recurrent LSTM to be applied to a sequence of inputs,
in two variants: a single layer and a multi-layer stack.
This operation automatically handles variable-length input.
The initial value of the hidden state and cell are 0.

Applying this layer to an input sequence
will return the sequence of the hidden states of the (top-of-stack) recurrent LSTM
(the LSTM's memory cell's value is not returned).
The returned sequence has the same length as the input.
If only the last state is desired, as in sequence-classification or some sequence-to-sequence scenarios,
use `BS.Sequences.Last()` to extract the last item's hidden state only.
(In a backward recurrence, you would use `BS.Sequences.First()`.)

To create a bidirectional model with `RecurrentLSTMLayer()`, use two layers, one with `goBackwards=true`,
and `Splice()` the two outputs together.
`RecurrentLSTMLayerStack()` currently does not support bidirectional models,
you must manually construct it using multiple `RecurrentLSTMLayer()/Splice()` combos.

#### Using the CuDNN5 RNN engine
This function will automatically use CuDNN5's optimized RNN engine if possible, that is, if

* the specified model is one that can be implemented by CuDNN5's function
  * no projection (no `cellShape` parameter)
  * no peep-hole connections
  * no self-stabilization
  * not going backwards
  * for `RecurrentLSTMLayerStack{}`, all layer dimensions have the same value
* `allowOptimizedEngine=true`

Specifically, CNTK requires to enable `allowOptimizedEngine=true`.
This is because the CuDNN5 RNN is implemented as a CNTK primitive operation that requires a GPU.
However, many real systems use GPUs for training but CPU-only servers in deployment.
The CuDNN5 RNN is not suitable here.
(It is theoretically possible to use the CuDNN5 RNN for training, and replace it for deployment
with an [editing operation](./BrainScript-Model-Editing.md) with an equivalent explicit LSTM implementation in BrainScript.)

#### Notes
If `allowOptimizedEngine=true` then these two layer variants
are wrappers around the [`OptimizedRNNStack()`](./OptimizedRNNStack.md) primitive.

### Example

A simple text classifier, which runs a word sequence through a recurrence and then
passes the *last* hidden state of the LSTM to a softmax classifer, could have this form:

    w = Input{...}                   # word sequence (one-hot vectors)
    e = EmbeddingLayer {150} (w)     # embed as a 150-dimensional dense vector
    h = RecurrentLSTMLayer {300} (e) # left-to-right LSTM with hidden and cell dim 300
    t = BS.Sequences.Last (h)        # extract last hidden state
    z = DenseLayer {10000, activation=Softmax} (t)  # softmax classifier

To change the above example to a 3-layer stack that uses the CuDNN5 RNN engine, change this line:

    h = RecurrentLSTMLayerStack {(300:300:300), allowOptimizedEngine=true} (e)

To create a bidirectional one-layer LSTM (e.g. using half the hidden dimension compared to above), use this:

    hFwd = RecurrentLSTMLayer {150} (e)
    hBwd = RecurrentLSTMLayer {150, goBackwards=true} (e)
    h = Splice (hFwd:hBwd)

## DelayLayer{}

Factory function to create a layer that delays its input.

    DelayLayer {T=1, defaultHiddenActivation=0}

### Parameters

* `T`: the number of time steps to delay. To access future values, use a negative value
* `defaultHiddenActivation`: value to use for the delayed frames at the boundaries

### Return Value

A function that implements the desired delay operation.

### Description

This operation delays an input sequence by `T` steps (default 1).
This useful, for example, to turn a word sequence into a sequence of overlapping word triples.

Consider an input sequence "a b c b", which shall be encoded as a sequence of one-hot vectors as follows:

    1 0 0 0
    0 1 0 1
    0 0 1 0

Here, every column is a one-hot vector and corresponds to a word.
Applying `DelayLayer{T=1}` to this input will generate this sequence:

    0 1 0 0
    0 0 1 0
    0 0 0 1

All tokens get delayed by one, and the first position gets filled in as a 0 vector.
Likewise, using `DelayLayer{T=-1}` (negative delay) will give access to the future values, and pad
from the right with a zero:

    0 0 0 0
    1 0 1 0
    0 1 0 0

#### Notes
This layer is a wrapper around the `PastValue()` and `FutureValue()` primitives.

### Example

The following shows how to stack three neighbor words into a trigram vector:

    x  = ...                   # input value, e.g. a N-dimensional one-hot vector
    xp = DelayLayer{} (x)      # previous value
    xn = DelayLayer{T-1} (x)   # next value (negative delay)
    tg = Splice (xp : x : xn)  # concatenate all into a 3N-dimensional three-hot vector

## BatchNormalizationLayer{}, LayerNormalizationLayer{}, StabilizerLayer{}

Factory functions to create layers for batch normalization, layer normalization, and self-stabilization.

    BatchNormalizationLayer {spatialRank = 0,
                             normalizationTimeConstant = 5000,
                             initialScale = 1, epsilon = 0.00001, useCntkEngine = true}
    LayerNormalizationLayer {initialScale = 1, initialBias = 0}
    StabilizerLayer{}

### Parameters

`BatchNormalizationLayer`:

* `spatialRank`: normalization parameters are pooled over the first `spatialRank` dimensions. Currently allowed
values are 0 (no pooling) and 2 (pooling across all pixel positions of an image)
* `normalizationTimeConstant` (default 5000): time constant in samples of the first-order low-pass filter
that is used to compute mean/variance statistics for use in inference
* `initialScale`: initial value of scale parameter
* `epsilon`: small value that gets added to the variance estimate when computing the inverse
* `useCntkEngine`: if true, use CNTK's native implementation. If false, use CuDNN's implementation (GPU only).

`LayerNormalizationLayer`:

* `initialScale`: initial value of scale parameter
* `initialBias`: initial value of bias parameter

### Return Value

A function that implements a layer that performs the normalization operation.

### Description

`BatchNormalizationLayer{}` implements the technique described in paper 
[Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift (Sergey Ioffe, Christian Szegedy)](http://arxiv.org/abs/1502.03167).
It normalizes its inputs for every minibatch by the minibatch mean/variance,
and de-normalizes it with a learned scaling factor and bias.

In inference, instead of using minibatch mean/variance,
batch normalization uses a long-term running mean/var estimate.
This estimate is computed during training by low-pass filtering minibatch statistics.
The time constant of the low-pass filter can be modified by the `normalizationTimeConstant` parameter.
We recommend to start with the default of (5000),
but experiment with other values, typically on the order of several thousand to tens of thousand.

`LayerNormalizationLayer{}` implements [Layer Normalization (Jimmy Lei Ba, Jamie Ryan Kiros, Geoffrey E. Hinton)](https://arxiv.org/abs/1607.06450).
It normalizes each input sample by subtracting the mean across all elements of the sample,
and then dividing by the standard deviation over all elements of the sample.

`StabilizerLayer{}` implements a self-stabilizer per [Self-stabilized deep neural network (P. Ghahremani, J. Droppo)](http://ieeexplore.ieee.org/document/7472719/).
This simple but effective technique multiplies its input with a learnable scalar (but unlike layer normalization,
it does not first normalize the input, nor does it subtract a mean).
Note that compared to the original paper, which proposes a linear scalar `beta` or an exponential one `Exp (beta)`,
we found it beneficial to use a sharpened softplus operation per the second author's suggestion,
which avoids both negative values and instability from the exponential.

#### Notes
`BatchNormalizationLayer{}` is a wrapper around the [`BatchNormalization()`](./BatchNormalization.md) primitive.
`LayerNormalizationLayer{}` and `StabilizerLayer{}` are expressed directly in BrainScript.

### Example

A typical layer in a convolutional network with batch normalization:

    MyLayer (x, depth, initValueScale) =
    {
        c = ConvolutionalLayer {depth, (5:5), pad=true, initValueScale=initValueScale} (x)
        b = BatchNormalizationLayer {spatialRank = 2} (c)    #####
        r = ReLU (b)
        p = MaxPoolingLayer {(3:3), stride = (2:2)} (r)
    }.p

## FeatureMVNLayer{}

Factory function to create a layer that normalizes feature input by their mean and standard deviation.

    FeatureMVNLayer{}

### Parameters

Empty argument list `{}`.

### Return Value

A function that implements a layer that performs the normalization operation.

### Description

This layer normalizes the input to a neural network by its bias and variance.
These values are estimated upfront by performing a full pass through the training data,
and then saved and frozen. This will happen automatically.

Since this layer's parameters are precomputed in a separate pass before the main training,
it can only be applied to variables declared as `Input{}`.

### Example

This is a typical beginning of a neural network for acoustic modelling of speech:

    features = Input{40}      # e.g. speech features
    featNorm = FeatureMVNLayer{} (features)
    h = DenseLayer{2048} (featNorm)
    ...

