---
title:   Simple Network Builder
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   08/03/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# Simple Network Builder

The `SimpleNetworkBuilder` allows to use simple standard network types with few lines of configuration. Only a limited set of configuration options is supported. More complex networks can be defined using the [`BrainScriptNetworkBuilder`](./BrainScript-Network-Builder.md) instead.

To use `SimpleNetworkBuilder`, include a configuration block of the same name in your training configuration. When an option is omitted the default value is assumed. We first provide a concise example and list all control parameters and options below.

    SimpleNetworkBuilder = [
        # 2 inputs, 2 hidden layers with 50 element nodes each, 2 outputs
        layerSizes        = 2:50*2:2
        trainingCriterion = "CrossEntropyWithSoftmax"
        evalCriterion     = "ErrorPrediction"
        layerTypes        = "Sigmoid"
        applyMeanVarNorm  = true
    ]

In the above example 'trainingCriterion' and 'layerTypes' could be omitted since they are using the default values. The following parameters are available:

* `initValueScale`: the value for scaling the range of the random numbers used for initialization. Default is `1`. If the model parameters are initialized using the uniform distribution, the random number range will be adjusted to `[-0.05 * initValueScale, 0.05 * initValueScale]`. If the model parameters are initialized using the Gaussian distribution, the standard deviation will be adjusted to `0.2 * initValueScale * fanout^(-1/2)`.

* `layerTypes`: the type of nonlinear operation in hidden layers. Valid values are `Sigmoid` (default), `Tanh`, and `RectifiedLinear`.

* `uniformInit`: determines whether to use uniform distribution to initialize model parameters. Valid values are `true` (default) and `false` (using Gaussian distribution to initialize model parameters).

* `applyMeanVarNorm`: whether to apply mean/variance normalization on the input. Valid values are `true` and `false` (default).

* `addDropoutNodes`: whether to add drop-out nodes. The default is `false`. If specified to `true`, a drop-out node will be applied to the input node and the output of every hidden layer.

* `layerSizes`: specifies the dimensions of layers. For instance, `layerSizes=128:10:200:4000` describes a neural network with two hidden layers. The first hidden layer has a dimension of 10, and the second hidden layer has a dimension of 200. The input and output layers have a dimension of 128 and 4000, respectively.

* `trainingCriterion`: the criterion used for training. The default is `CrossEntropyWithSoftmax`. Alternatives are `SquareError`, `CrossEntropy`, and `ClassBasedCrossEntropyWithSoftmax`. The `ClassBasedCrossEntropyWithSoftmax`
is for class-based training, which would be useful if the output dimension is large and therefore need to be split into classes to speed-up the training and evaluation.

* `evalCriterion`: the criterion for evaluation. The selection of values are the same as the `trainingCriterion`.

* `lookupTableOrder`: specifies the order of context expanding in the lookupNode. The default value is `1`. Setting it to a value such as 3 would expand the input dimension in a context-dependent way by an order of 3. For example, if the input observation has a dimension of 20, setting this value to 3 would set the input node dimension to 60. 

For recurrent neural networks (RNNs), there are additional parameters.

* `recurrentLayer`: specifies the layers that contain self recurrent connections. By default there is no recurrent layer. Use the syntax `n1:n2:n3` to specify that layers n1, n2, and n3 have recurrent connections.

* `defaultHiddenActivity`: the default hidden layer activity value used by the delay node when accessing values before the first observation. The default value is `0.1`.

* `rnnType`: the type of predefined networks. Valid values are:
  * `SIMPLENET`: the feed-forward neural network. This is the default network type.
  * `SIMPLERNN`: the simple RNN, which may be a deep RNN in which several layers have recurrent loops.
  * `CLASSLM`: the class-based simple RNN. It uses sparse input, sparse parameter and sparse output. This is often used for language modeling tasks.
  * `LBLM`: the log-bilinear neural network.
  * `LSTM`: the long short-term memory neural network.
  * `CLASSLSTM`: the class-based long short-term memory neural network. It uses sparse input, sparse parameter and sparse output. This is often used for language modeling tasks.

Next: Define your own complex networks with [`BrainScriptNetworkBuilder`](./BrainScript-Network-Builder.md).
