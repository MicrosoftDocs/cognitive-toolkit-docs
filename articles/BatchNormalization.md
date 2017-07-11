---
title:   Batch normalization 
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   08/28/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---
# Batch normalization

    BatchNormalization(input, scale, bias, runMean, runVariance, spatial,
                       normalizationTimeConstant = 0,
                       blendTimeConstant = 0, epsilon = 0.00001, useCntkEngine = true)

## Parameters

* `input` is the input of the batch normalization node
* `scale` is a [ParameterTensor{}](./Parameters-And-Constants.md#parametertensor) that holds the learned componentwise-scaling factors (`gamma` term in the equation below).
* `bias` is a [ParameterTensor{}](./Parameters-And-Constants.md#parametertensor) that holds the learned bias (`beta` term). `scale` and `bias` must have the same dimensions which must be equal to the `input` dimensions in case of `spatial = false` or number of output convolution feature maps in case of `spatial = true`.
* `runMean` is the running mean which is used during evaluation phase and might be used during training as well.
You must pass a [ParameterTensor{}](./Parameters-And-Constants.md#parametertensor) with
the same dimensions as `scale` and `bias`,
initial value 0, and `learningRateMultiplier=0` set.
* `runVariance` is the running variance. It is represented the same way as `runMean`.
* `spatial` is a flag that specifies whether to compute mean/var for each feature in a minibatch independently or, in case of convolutional layers, per feature map.
* `normalizationTimeConstant` (default 0): time constant for computing running average of mean and variance as a
low-pass filtered version of the batch statistics. Note: The default is not typically what you want.
* `blendTimeConstant` (default 0): allows to smooth batch estimates with the running statistics
* `epsilon` is a conditioner constant added to the variance when computing the inverse standard deviation.
* `useCntkEngine` (default: true): set this to `false` to select the GPU-only cuDNN implementation

## Return value

The batch-normalized `input`.

## Description

`BatchNormalization` implements the technique described in paper 
[Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift (Sergey Ioffe, Christian Szegedy)](http://arxiv.org/abs/1502.03167).
In short, it normalizes layer outputs for every minibatch for each output (feature) independently and applies affine transformation to preserve representation of the layer. That is, for layer `input`:

```
m = mean (input)
var = variance (input)
inputNorm = (input - mean)/sqrt (epsilon + var)
output = gamma * inputNorm + beta
```

where `gamma` and `beta` are trainable parameters.

`mean` and `variance` are estimated from training data. In the simplest case, they are the mean and variance
of the current minibatch during training. In inference, a long-term estimate is used instead.

The long-term estimates are a low-pass-filtered version of the minibatch statistics, with the time constant
(in samples) given by the `normalizationTimeConstant` parameter.
A value of `0` means there will be no exponential smoothing and running mean/variance
will always be equal to those of the *last seen minibatch*.
This is often undesirable.
Instead, it is recommended to use a value of a few thousand here.
The `BatchNormalizationLayer{}` wrapper has a default of 5000.

For more information about time constants and exponential smoothing: https://en.wikipedia.org/wiki/Exponential_smoothing#Time_Constant

Because minibatch statistics can be noisy,
CNTK also allows to use a MAP (maximum-a-posteriori) estimate during training,
where the running long-term estimate is taken as the prior.
The weight of the prior is controlled by the `blendTimeConstant` parameter.
However, this has not been found useful so far in our experiments.

Note that during inference, CNTK will set both time constants automatically such that
only use the existing running mean is used, and that it is not updated. There is no explicit action needed by the user.

### cuDNN implementation

By default, this function uses a CNTK implementation which works with both GPUs and CPUs. You can choose to use the cuDNN implementation, which is more performant. Note, however, that the cuDNN implementation does not support all options, and training requires a GPU (CNTK does, however, provide a CPU emulation for inference even if you select the cuDNN implementation).
