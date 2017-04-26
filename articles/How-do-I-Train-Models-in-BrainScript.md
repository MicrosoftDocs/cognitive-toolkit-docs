"---
title:   How do I train models in BrainScript
author:    chrisbasoglu
date:    04/12/2017
ms.author:   cbasoglu
ms.date:   04/12/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# How do I train models in BrainScript"
* [Perform layer-wise training](#layer-wise-training)? 
* [Train with a multitask objective](#train-with-a-multitask-objective)? 
* [Train a regression model on images](#train-a-regression-model-on-images)? 
* [Train a multilabel classifier](#train-a-multilabel-classifier)?  
* [Get started in sequence to sequence modelling](#get-started-in-sequence-to-sequence-modelling)? 
* [Train a DSSM (or a convolutional-DSSM) model](#train-a-dssm-or-a-convolutional-dssm-model)?  
* [Train an Image auto encoder using Deconvolution and Unpooling](./Image-Auto-Encoder-Using-Deconvolution-And-Unpooling.md)?
* [Object Detection using Fast R-CNN](./Object-Detection-using-Fast-R-CNN.md)? 

## Layer wise training

To perform layer-wise training simply use multiple "commands" in your config file, where each command is of type action=train.
```
command = TrainLayer1:TrainLayer2:TrainLayer3:EndToEndTrain:Test:Output

TrainLayer1= [ 
   action = train
   ...
]

TrainLayer2= [ 
   action = train
   ...
]
...
```

## Train with a multitask objective

You can just define your combined criterion as a BrainScript expression and you can monitor all individual task losses by specifying them as `evaluationNodes`.
```
task1loss = CrossEntropyWithSoftMax(prediction,label)
task2loss = SquareError(reconstruction,input)
mtloss = task1loss + Constant(0.3) .* task2loss 
criterionNodes = (mtloss)
evaluationNodes = (task1loss:task2loss)
```

## Train a regression model on images

Below we describe how to predict one or more floating point value for an input image using CNTK. An example use case is to predict a bounding box, for example as (x, y, w, h), of an object in a given image. You could also think of predicting the price for a car just by looking at an image of that car (would be interesting actually). Here we use a very simple example in which we train a network to predict the average rgb values of an image (normalized to [0, 1]). However, the same steps apply for other use cases. These steps are:

1. Define both the image and the ground truth regression labels as inputs to your network
2. Define a network that predicts a matching number of values wrt your regression labels
3. Define a loss function that compares the predicted values with the ground truth
4. Adapt the reader section in your .cntk config file to read both image and regression labels

Here is how you could do it. The full config file is included in the Examples folder at [Examples/Image/Regression/RegrSimple_CIFAR10.cntk](https://github.com/Microsoft/CNTK/blob/master/Examples/Image/Regression/RegrSimple_CIFAR10.cntk). That folder also contains the scripts to download the image data and generate the regression ground truth for training and testing.

1-3) Defining inputs, network and loss function:

```
    BrainScriptNetworkBuilder = [
        imageShape = 32:32:3
        featScale = Constant(1/256)
        labelDim = 3

        model (features) = {
            featNorm = Scale(features, featScale)
            h1 = LinearLayer {100,      init="gaussian", initValueScale=1.5} (featNorm)
            ol = LinearLayer {labelDim, init="gaussian", initValueScale=1.5} (h1)
        }.ol

        # inputs
        features = Input {imageShape}
        regrLabels = Input {labelDim}
        
        # apply model to features
        ol = model (features)

        # define regression loss
        # rmse = sqrt(SquareError(regrLabels, ol) / labelDim)
        sqerr = SquareError (regrLabels, ol)
        rmse = Sqrt (Constant(1/labelDim).* sqerr)

        featureNodes    = (features)
        labelNodes      = (regrLabels)
        criterionNodes  = (rmse)
        evaluationNodes = (rmse)
        OutputNodes     = (ol)
    ]
```

4) Defining a composite reader using both the ImageReader and CNTKTextFormatReader:

```
    reader = {
        verbosity = 0 ; randomize = true
        deserializers = ({
            type = "ImageDeserializer" ; module = "ImageReader"
            file = "$dataDir$/cifar-10-batches-py/train_map.txt"
            input = {
                features = { transforms = (
                    { type = "Scale" ; width = 32 ; height = 32 ; channels = 3 ; interpolations = "linear" } :
                    { type = "Transpose" }
                )}
                ignored = { labelDim = 10 }
            }
        } : {
            type = "CNTKTextFormatDeserializer" ; module = "CNTKTextFormatReader"
            file = "$dataDir$/cifar-10-batches-py/train_regrLabels.txt"
            input = {
                regrLabels = { dim = 3 ; format = "dense" }
            }
        })
    }
```

The reader is a composite reader that uses the ImageReader to read images and the CNTKTextFormatReader to read the regression ground truth labels. It does so by defining an array of deserializers (using `{...} : {...}`) and assigning the inputs as defined in the network above (cf. features and regrLabels).

See [Examples/Image/Miscellaneous/CIFAR-10/06_RegressionSimple.cntk](https://github.com/Microsoft/CNTK/blob/master/Examples/Image/Miscellaneous/CIFAR-10/06_RegressionSimple.cntk) for the full config file and the corresponding Readme in that folder for running the example.

## Train a multilabel classifier

For multilabel classification you should avoid using CrossEntropy as it can only handle input vectors that sum to 1. A sensible alternative is to use a sum of logistic loss functions, one for each output
```
...
probabilities = DenseLayer {outputSize, activation=Sigmoid} (hidden)
logisticLoss = Logistic (multiLabels, probabilities)
trainingCriterion = (logisticLoss)
...
```
Apart from the loss itself you might want to monitor other metrics such as the number of incorrect predictions. There is no builtin expression for this but it can be expressed as
```
...
hammingLoss (y, p) = ReduceSum (y != (p > 0.5))
hl = hammingLoss(multiLabels,probabilities)
evaluationNodes = (hl)
...
```
This counts the number of times y[i] disagrees with p[i]>0.5. 

## Get started in sequence to sequence modelling

This [hands-on lab](Hands-On-Labs-Language-Understanding.md) describes the main ingredients for getting started on sequence processing such as the CNTK text format and how to configure the reader to use short aliases for the various input sequences.
The [grapheme-to-phoneme (G2P) example](https://github.com/Microsoft/CNTK/blob/master/Examples/SequenceToSequence/CMUDict/BrainScript/G2P.cntk)
demonstrates an actual sequence-to-sequence task.

An important issue for sequence-to-sequence modeling is how to decode test data with beam search.
This can be done with in a section of your config where the top level action is "write". Decoding requires a search for the most probable sequence of outputs. CNTK has a [beam search](https://en.wikipedia.org/wiki/Beam_search) decoder while you can call like this
```
BrainScriptNetworkBuilder = (BS.Seq2Seq.BeamSearchSequenceDecoderFrom (
                                        BS.Network.Load (decodeModelPath), beamSize))
```
and will execute beam search with the specified beam size. For a beam size of 1 there is a specialized greedy decoder
```
BrainScriptNetworkBuilder = (BS.Seq2Seq.GreedySequenceDecoderFrom (
                                        BS.Network.Load (decodeModelPath)))
```
Both decoders have specific requirements to the network, as shown
in the [G2P example](https://github.com/Microsoft/CNTK/blob/master/Examples/SequenceToSequence/CMUDict/BrainScript/G2P.cntk)

## Train a DSSM (or a convolutional DSSM) model

[DSSM](https://www.microsoft.com/en-us/research/project/dssm/) (or Deep Semantic Similarity Model) is a DNN model trained on pairs of source-target texts, for learning a short-text embedding space where relevant source and target text pairs are closer. The text input to the model is represented by their pre-computed trigram hash (see, [Huang et al.](https://www.microsoft.com/en-us/research/publication/learning-deep-structured-semantic-models-for-web-search-using-clickthrough-data/)). For [C-DSSM](https://www.microsoft.com/en-us/research/publication/learning-semantic-representations-using-convolutional-neural-networks-for-web-search/), the trigram hash is computed per word and then concatenated in the order in which the words occur in the text. The input to both models are of fixed size. If we consider 50K trigrams, then the DSSM input corresponding to the source and the target text would be a vector of length 50K each. For C-DSSM, the vector would be of length 50K x n, where the first n-1 word vectors are concatenated, and the nth vector contains a sum of the vectors corresponding to all the remaining words in the text. If there are less than n words in the text, then the rest of the vector is padded with zeros. To draw an analogy with image, you can think of the text input for C-DSSM as an image with dimensions 10x1 and 50K channels stored in a `[C x H x W]` format.

This example demonstrates how to train a DSSM / C-DSSM model using CNTKTextFormatReader. The data should contain 2 features (source and target text) and 1 label (which is always set to the value 1 in the training data as it contains only positive samples – during training the negative target examples are generated by random sampling).
Here’s the reader configuration,
```
reader = {
    verbosity   = 0
    randomize   = true

    deserializers = ({
        type    = "CNTKTextFormatDeserializer"
        module  = "CNTKTextFormatReader"
        file    = "data.txt"

        input = {
            Q   = { dim = 500000; format = "sparse" }
            D   = { dim = 500000; format = "sparse" }
            L   = { dim = 1;      format = "dense" }
        }
    })
}
```
A sample of the input data,
```
|L 1 |Q 482:1 761:1 1832:1 2117:1 12370:1 17131:1 17854:1 24976:1 27676:1 28055:1 28177:1 29507:1|D 482:1 761:1 1832:1 2117:1 12370:1 17131:1 17854:1 24976:1 27676:1 28055:1 28177:1 29507:1
|L 1 |Q 149:1 153:1 595:1 671:1 675:1 1110:1 1517:1 2077:1 2114:1 5533:1 5662:1 6886:1 6901:1 7294:1 12846:1 13033:1 16614:1 19425:1 22015:1 24839:1 24994:1 26196:1 26358:1 27565:1|D 149:1 153:1 595:1 671:1 675:1 1110:1 1517:1 2077:1 2114:1 5533:1 5662:1 6886:1 6901:1 7294:1 12846:1 13033:1 16614:1 19425:1 22015:1 24839:1 24994:1 26196:1 26358:1 27565:1
|L 1 |Q 187:1 2294:1 2800:1 6920:1|D 187:1 2294:1 2800:1 6920:1
```
And finally the network definition,
```
BrainScriptNetworkBuilder = {
    # Constants scalars
    isConvolutional     = true
    numWords            = (if isConvolutional then 10 else 1)
    numTrigramsPerWord  = 50000
    numHiddenNodes      = 300
    wordWindowSize      = 3
    numWindows          = numWords - wordWindowSize + 1
    numNeg              = 50

    # Constant tensors
    CONST_GAMMA     = Constant(10)
    CONST_SHIFT     = Constant(1)
    CONST_NEG       = Constant(numNeg)
    CONST_PAD_NEG   = Constant(0, rows=numNeg, cols=1)
    CONST_PAD_POS   = Constant(1, rows=1, cols=1)
    CONST_PAD       = Splice(CONST_PAD_POS : CONST_PAD_NEG, axis=1)

    # Inputs
    Q   = Input(500000)
    D   = Input(500000)
    L   = Input(1)

    qr      = if isConvolutional
                then TransposeDimensions(ReshapeDimension(Q, 1, numTrigramsPerWord:1:numWords), 1, 3)
                else Slice(0, numTrigramsPerWord, Q, axis=1)
    dr      = if isConvolutional
                then TransposeDimensions(ReshapeDimension(D, 1, numTrigramsPerWord:1:numWords), 1, 3)
                else Slice(0, numTrigramsPerWord, D, axis=1)

    qdssm   = Sequential (
                DenseLayer {numHiddenNodes, activation=Tanh} :
                DenseLayer {numHiddenNodes, activation=Tanh} :
                DenseLayer {numHiddenNodes, activation=Tanh})
    qcdssm  = Sequential (
                ConvolutionalLayer {numHiddenNodes, (wordWindowSize:1), pad=false, activation=Tanh} :
                MaxPoolingLayer {(numWindows:1), stride=(1:1)} :
                DenseLayer {numHiddenNodes, activation=Tanh} :
                DenseLayer {numHiddenNodes, activation=Tanh})
    ddssm   = Sequential (
                DenseLayer {numHiddenNodes, activation=Tanh} :
                DenseLayer {numHiddenNodes, activation=Tanh} :
                DenseLayer {numHiddenNodes, activation=Tanh})
    dcdssm  = Sequential (
                ConvolutionalLayer {numHiddenNodes, (wordWindowSize:1), pad=false, activation=Tanh} :
                MaxPoolingLayer {(numWindows:1), stride=(1:1)} :
                DenseLayer {numHiddenNodes, activation=Tanh} :
                DenseLayer {numHiddenNodes, activation=Tanh})
    qembed  = if isConvolutional
                then qcdssm
                else qdssm
    dembed  = if isConvolutional
                then dcdssm
                else ddssm

    qf  = qembed(qr)
    df  = dembed(dr)
    lf  = Times(CONST_PAD, L)
    c   = CosDistanceWithNegativeSamples(qf, df, CONST_SHIFT, CONST_NEG)
    s   = Slice(0, 1, c, axis=1, tag="output")
    ce  = CrossEntropyWithSoftmax(lf, Scale(CONST_GAMMA, c), tag="criterion")
}
```

Note:
* While C-DSSM has been shown to consistently perform better than DSSM, it also trains slower (sometime up to 5-10x slower). So in some cases you may get better performance from DSSM in the same training time by training over more data (or for more epochs).
* The original DSSM / C-DSSM were trained on query and document title pairs. But you can learn other relationships between short texts by training on other kinds of data such as [session query pairs](https://www.microsoft.com/en-us/research/publication/exploring-session-context-using-distributed-representations-of-queries-and-reformulations/) or [query prefix-suffix pairs](https://www.microsoft.com/en-us/research/publication/query-auto-completion-for-rare-prefixes/).

## Train an Image Auto Encoder Using Deconvolution And Unpooling

There are instructions [here](./Image-Auto-Encoder-Using-Deconvolution-And-Unpooling.md).

## Train Object Detection using Fast R CNN

There are instructions [here](./Object-Detection-using-Fast-R-CNN.md).

