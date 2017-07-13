---
title:   BrainScript SGD Block
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   06/01/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# BrainScript SGD Block

The SGD configuration block controls the behavior of the SGD (Stochastic Gradient Descent) algorithm in CNTK.
If you are familiar with other toolkits, be sure to check out
* [How is the minibatch size defined in CNTK](./BrainScript-minibatchSize-and-Python-minibatch_size_in_samples-in-CNTK.md)
* [How is the epoch size defined in CNTK](./BrainScript-epochSize-and-Python-epoch_size-in-CNTK.md)
* [How to convert learning rate and momentum parameters from other toolkits?](#converting-learning-rate-and-momentum-parameters-from-other-toolkits)

The SGD configuration block has the following structure and default values:

    SGD = {
        # Training process control
        modelPath = ...
        trainCriterionNodeName = ...
        evalCriterionNodeName = ...

        maxEpochs = ...
        epochSize = 0
        minibatchSize = 256

        truncated = false

        dropoutRate = 0
        maxTempMemSizeInSamplesForCNN = 0
        keepCheckPointFiles = false

        disableWkInBatchNormal = false

        # Learning rate and momentum control
        learningRatesPerSample = ...
        learningRatesPerMB = ...
        minLearningRatePerSample = ...

        momentumAsTimeConstant = ...
        momentumPerMB = ...

        useNAG = false

        autoAdjust = {
            autoAdjustLR = "none"  # | "searchBeforeEpoch" | "adjustAfterEpoch"
            autoAdjustMinibatch = false

            # for autoAdjustLR = "adjustAfterEpoch":
            reduceLearnRateIfImproveLessThan = 0
            learnRateDecreaseFactor = 0.618
            increaseLearnRateIfImproveMoreThan = infinity
            learnRateIncreaseFactor = 1.382
            loadBestModel = true
            learnRateAdjustInterval = 1
            useCVSetControlLRIfCVExists = true
            useEvalCriterionControlLR = false

            # for autoAdjustLR = "searchBeforeEpoch":
            numMiniBatch4LRSearch = 500
            numPrevLearnRates = 5
            numBestSearchEpoch = 1

            # for autoAdjustMinibatch = true:
            numMiniBatch4LRSearch = 500
            minibatchSizeTuningFrequency = 1
            minibatchSizeTuningMax = 1048576
            minibatchSearchCriterionErrorMargin = 1
        }

        parallelTrain = {
            parallelizationMethod =  "none"  # | "dataParallelSGD" | "blockMomentumSGD" | "modelAveragingSGD"
            parallelizationStartEpoch = 1
            distributedMBReading = false
            syncPerfStats = 0
            # for parallelizationMethod = "dataParallelSGD"
            dataParallelSGD =
            {
                gradientBits = (8*sizeof(precision))  # | 1 | 2
                useBufferedAsyncGradientAggregation= false
            }
            # for parallelizationMethod = "blockMomentumSGD"
            blockMomentumSGD = {
                blockSize = (120000 * #workers)
                blockMomentumAsTimeConstant = (-blockSize / log(1 - 1/#workers))
                resetSGDMomentum = true;
                useNesterovMomentum = true;
                blockLearningRate = 1.0
            }
            # for parallelizationMethod = "modelAveragingSGD"
            modelAveragingSGD = {
                blockSize = (40000 * #workers)
            }
        }
    
        # Gradient control
        gradientClippingWithTruncation = true
        clippingThresholdPerSample = (infinity)
        L2RegWeight = 0
        L1RegWeight = 0
        gaussianNoiseInjectStd = 0
        gradUpdateType = ""  # "" | "adagrad" | "rmsProp" | "fsAdaGrad"
        # for gradUpdateType = "adaGrad" or "rmsProp":
        normWithAveMultiplier = true
        # for gradUpdateType = "rmsProp":
        rms_wgt_inc = 1.2
        rms_wgt_dec = 0.75
        rms_wgt_max = 10.0
        rms_wgt_min = 0.1
        rms_gamma = 0.99

        # Information display
        traceLevel = 0
        firstMBsToShowResult = 10
        numMBsToShowResult = 10
        numMBsToCUDAProfile = 0

        # Precompute
        useAllDataForPreComputedNode = true

        # Gradient check
        gradientCheck = false
        sigFigs = 6
    }

## Parameters

### Training process control

* `trainCriterionNodeName`: the name of the training criterion node. If not provided the default training criterion node in the network will be used.

* `evalCriterionNodeName`: the name of the evaluation criterion node. If not provided the default evaluation criterion node in the network will be used.

* `epochSize`: [click here to learn about epochSize](./BrainScript-epochSize-and-Python-epoch_size-in-CNTK.md)

* `keepCheckPointFiles`: whether you want to keep the check point file after a new epoch starts. Valid values are `true` and `false` (default).

* `disableWkInBatchNormal`: whether to enable the weight decay term of batch normalization while SGD updates. Valid values are `true` and `false` (default).

* `maxEpochs`: maximum number of epochs to run.

* `minibatchSize`: [click here to learn about minibatchSize](./BrainScript-minibatchSize-and-Python-minibatch_size_in_samples-in-CNTK.md)

* `dropoutRate`: dropout rate during the training procedure. Default is `0.0`. Can use syntax such as 0.5*10:0.2 which means using dropout rate 0.5 for 10 epochs and then 0.2 for the rest.

* `maxTempMemSizeInSamplesForCNN`: maximum temporary memory used (in number of samples) when packaging and unpackaging input features. Default is 0, which means using any value as needed. Useful to control the memory foot print esp. when run under GPU.

* `saveBestModelPerCriterion`: option to save the best model for each criterion as measured on cross validation data. When training ends models are named `<modelName>_<criterionName>`. Valid values are `true` and `false` (default).

### Learning rate and momentum control

Note CNTK's way of specifying learning rates and momentum differs from other toolkits.
[See here](#converting-learning-rate-and-momentum-parameters-from-other-toolkits) for a detailed description.

* `learningRatesPerSample`: the learning rates per epoch with which each sample's gradient updates the model. You can use different values for different epochs, e.g., 0.025*10:0.00625 means use the learning rate 0.025 for the first 10 epochs and then 0.00625 for the rest. 
This is the preferred way of specifying in CNTK, since it specifies the learning rates agnostic to the
minibatch size, which is important when automatic minibatch-sizing is used.
Other toolkits often specify learning rates in a minibatch-averaging fashion.
To convert from that notation, use learning rate per sample = learning rate per MB / `minibatchSize`
([see here](#converting-learning-rate-and-momentum-parameters-from-other-toolkits) for more details).

* `learningRatesPerMB`: alternative way of specifying learning rates to be applied to the *average* over samples in the minibatch.
This is the most common way of specifying learning rates in other toolkits, but is problematic
in CNTK's where data-parallel training, which modifies the minibatch size.
Internally, this will be converted into `learningRatesPerSample` by dividing the values by the specified 'minibatchSize'.
Mutually exclusive with `learningRatesPerSample`.

* `minLearningRatePerSample`: minimum learning rate per sample. When the learning rate per sample is smaller than this value the training process will terminate. This is often used to control early stopping when automatic learning rate adjustment is enabled. Default is 1e-9.

* `momentumAsTimeConstant`: similarly to `learningratesPerSample`, CNTK specifies momentum in a minibatch-size agnostic way as the [time constant](https://en.wikipedia.org/wiki/Time_constant) (in samples) of a unit-gain 1st-order IIR filter. The value specifies the number of samples after which a gradient has an effect of 1/e=37%.
Other toolkits often specify momentum as a per-minibatch weight (e.g. 0.9).
To convert from that, use `momentumAsTimeConstant = -minibatchSize / ln (momentumPerMB)`.
You can use syntax such as 20000*10:2500 which means using the momentum time constant 20000 for 10 epochs and then 2500 for the rest.

* `momentumPerMB`: this alternative way of specifying momentum
mimics the behavior of common toolkits. E.g. specifying 0.9 means that the previous gradient will be retained with a weight of 0.9. Note, however, that, unlike some other toolkits, CNTK still uses a unit-gain filter, i.e. the new gradient will be multiplied with `(1-momentumPerMB)`. 
Internally, this will be converted into `momentumAsTimeConstant = -minibatchSize / ln (momentumPerMB)`.

* `autoAdjust`: contains the information related to the automatic learning rate control. Default value is empty (“”) which means no automatic learning rate control. Inside the block, there can be following values:
  * `autoAdjustLR`: the automatic learning rate adjustment algorithm to use. Valid values are `None` (default, don’t auto adjust learning rate), `AdjustAfterEpoch` (check the training criterion after each epoch using the development set of the training set and decide whether to adjust the learning rate), and `SearchBeforeEpoch` (search the learning rate based on a small portion of the training set before each epoch starts).

  * When used in the `AdjustAfterEpoch` mode:
    * `reduceLearnRateIfImproveLessThan`: reduce the learning rate if the improvement is less than this value. Default is `0`.
    * `learnRateDecreaseFactor`: the learning rate decrease factor. Default value is `0.618`.
    * `increaseLearnRateIfImproveMoreThan`: increase the learning rate if the improvement is larger than this value. Default value is `1#INF` (infinity) which means never increase.
    * `learnRateIncreaseFactor`: the learning rate increase factor. Default value is `1.382`.
    * `loadBestModel`: whether to load the best model if the current model decreases the performance. Valid values are `true` (default) and `false`.
    * `learnRateAdjustInterval`: determine the frequency of applying the learning rate adjustment check. Default is `1` epoch. If this value is set to a value larger than 1 the learning rate adjustment will be based on the average criterion computed from the last `learnRateAdjustInterval` epochs.
    * `useEvalCriterionControlLR`: use evaluation criterion instead of the training criterion to control the learning rate. By default it's false.

  * When used in the `SearchBeforeEpoch` mode.
    * `numMiniBatch4LRSearch`: the number of minibatches used to search the learning rate. Default value is `500`. It’s typically set to 10-20% of the total minibatches in an epoch.
    * `numPrevLearnRate`: number of previous learning rates used as a hint to the search range. Default value is `5`.
    * `numBestSearchEpoch`: number of epochs in which we use the best learning rate instead of the sufficient learning rate . Default value is `1`.

  * When used in the 'AdaptiveMinibatchSizing' mode.
    * `numMiniBatch4LRSearch`: the number of minibatches used to search the minibatch size when in adaptive minibatch size mode. Default value is `500`. It’s typically set to 10-20% of the total minibatches in an epoch this is shared with the search for learning rate in `SearchBeforeEpoch` mode.
    * `autoAdjustMinibatch`: enable or disable whether minibatch size is adaptively adjusted. Default value is `false`. Adaptive minibatch sizing will begin on epochs starting after user minibatch sizes explicitly specified are complete. For example if the user specified minibatchSize=256:1024, then 256 and 1024 are used in the first 2 Epochs and adaptive minibatch sizing is used afterwards.
    * `minibatchSizeTuningFrequency`: The number of epochs to skip, on a periodic basis, before dynamically adjusting the minibatch size. Default value is `1`.
    * `minibatchSizeTuningMax`: The maximum size allowed for an adaptively adjusted minibatch size. Default value is `1048576`.

### Gradient control

* `gradientClippingWithTruncation`: whether to use the truncation based gradient clipping to control gradient explosion. Valid values are `true` (default) and `false`. If it is false the norm based clipping will be used instead which is more expensive.

* `clippingThresholdPerSample`: the clipping threshold for each sample. Default value is `1#INF` which means infinity (i.e., clipping is turned off).

* `L2RegWeight` (default 0): the L2 regularization weight per sample.
The Frobenius norm of the learnable parameter is added to the objective with this weight.
This is specified *per sample*, meaning that the Frobenius norm
is multiplied by the number of samples in the minibatch.

* `L1RegWeight` (default 0): the L1 regularization weight per sample.

* `gradUpdateType`: gradient update type. Valid values are `None` (default, no special treatment to the gradient), `AdaGrad`, and `RmsProp`. 
  * When `gradUpdateType` equals to `AdaGrad` or `RmsProp`, you can control the behavior of the gradient update using following parameters:
    * `normWithAveMultiplier`: normalize the gradient with the average multipliers applied to the gradients by the AdaGrad/RmsProp algorithm. Default is `true` (default). 
  * When `gradUpdateType` equals to `RmsProp`, you can control the behavior of the gradient update using following parameters:
    * `rms_wgt_inc`: multiplicative increment of the learning rate scale. Default is `1.2`.
    * `rms_wgt_dec`: multiplicative decrement of the learning rate scale. Default is `0.75`.
    * `rms_wgt_max`: maximum learning rate scale allowed. A value closer to 1 makes the learning rate adjustment more stable but slower. Default is `10`.
    * `rms_wgt_min`: minimum learning rate scale allowed. A value closer to 1 makes the learning rate adjustment more stable but slower. Default is `0.1`.
    * `rms_gamma`: smoothing factor used to estimate the moving average of the variance. The smaller the value, the quicker it forgets the past information. Default is `0.99`.

* `gaussianNoiseInjectStd`: the standard deviation of the Gaussian noise added when using the `AdaGrad` approach. Default is `0`.

### Information display
* `traceLevel`: trace level to decide what information to print out in the stderr. Valid values are `0` (default) and `1`.

* `numMBsToShowResult`: display training statistics after how many minibatches. Default is `10`.

### TensorBoard

* `tensorBoardNumMBsToLogResult`: number of minibatches between logging results to TensorBoard.

### Gradient Check
* `gradientCheck`: determines whether to use the gradient checker. The default value is `false`. When using the gradient checker you need to use a minibatch size that is larger than the sequence length for RNNs due to the truncated backpropagation through time (BPTT) algorithm used to train RNNs, and a smaller learning rate to prevent numerical issues caused by divergence. In addition, precision should be set to double.

## Description

The behavior of the SGD algorithm (Stochastic Gradient Descent Learner) is controlled by the SGD block of the options. When an option is omitted the default value is assumed.

Parameters that are not explicitly specified are left to the default values.

### Specifying data sizes
* [What is the minibatch size in CNTK?](./BrainScript-minibatchSize-and-Python-minibatch_size_in_samples-in-CNTK.md)
* [What is the epoch size in CNTK?](./BrainScript-epochSize-and-Python-epoch_size-in-CNTK.md)

### Converting Learning-rate and Momentum Parameters From Other Toolkits

CNTK's model-update formulae differ somewhat from some other toolkits and from literature, in that in CNTK, the parameters are specified in a way that is *agnostic of the minibatch size*. This is important in the context of data-parallel training, where CNTK itself may modify the minibatch size. Specifying learning rate and momentum in an agnostic way avoids complexities of adjusting these values upon changes of minibatch size.

These are CNTK's model-update formulae for SGD with momentum:

    G(t) = (1-mu) sum { g(t-minibatchSize+1) ... g(t) } + mu * G(t-minibatchSize)
    mu   = exp (-minibatchSize/momentumAsTimeConstant)
    M(t) = M(t-minibatchSize) + learningRatePerSample G(t)

with

* `G(t)`: momentum-smoothed gradient after `t` samples
* `g(t')`: raw gradient of sample at time `t'`
* `M(t)`: model used after seeing `t` samples.
* `t` incrementing in steps of `minibatchSize`

(Note: When using variable-length sequences, `minibathSize` will slightly fluctuate since sequence lengths
in a minibatch generally do not sum up precisely to the requested `minibathSize`.)

You notice:

* The momentum filter `G(t)` is unit-gain. Every sample's gradient is distributed over time such that their sum is 1.
* The learning rate is specified per sample, rather than w.r.t. an average over samples.

The specification used in other toolkits and neural-network literature is often this:

    G'(t) = average { g(t-minibatchSize+1) ... g(t) } + mu * G'(t-minibatchSize)
    M(t) = M(t-minibatchSize) + eta G'(t)

with

* `G'`: gradient defined in the alternative way as a per-minibatch *average* and without `(1-mu)`
* `mu`: momentum parameter, e.g. 0.9, of a *non-unit-gain* IIR filter, applied *per minibatch*
* `eta`: learning rate with minibatch-*average* gradient

Parameters specified in this way can be mapped to CNTK parameters using these formulas:

    learningRatePerSample = eta / minibatchSize / (1-mu)
    momentumAsTimeConstant = -minibatchSize / ln (mu)

You will get close to this by using `learningRatePerMB` and `momentumPerMB`, which are mapped as follows (notice the absence of `/ (1-mu)` for `learningRatePerSample`:

    learningRatePerSample = learningRatePerMB / minibatchSize
    momentumAsTimeConstant = -minibatchSize / ln (momentumPerMB)

## Example

Configuration used by the [ImageHandsOn tutorial](./Hands-On-Labs-Image-Recognition.md) with data parallelism
and automatic minibatch scaling:

    SGD = {
        epochSize = 50000
    
        maxEpochs = 160 ; minibatchSize = 128
        learningRatesPerSample = 0.0078125*80:0.00078125*40:0.000078125
        momentumAsTimeConstant = 1200
        L2RegWeight = 0.0001
    
        firstMBsToShowResult = 10 ; numMBsToShowResult = 500

        parallelTrain = {
            parallelizationMethod = "dataParallelSGD"
            parallelizationStartEpoch = 1
            distributedMBReading = true
            dataParallelSGD = { gradientBits = 2 }
        }
        autoAdjust = {
            autoAdjustMinibatch = true        # enable automatic growing of minibatch size
            minibatchSizeTuningFrequency = 10 # try to enlarge after this many epochs
            numMiniBatch4LRSearch = 200
            minibatchSizeTuningMax = 15000    # out of memory above this
        }
    }
