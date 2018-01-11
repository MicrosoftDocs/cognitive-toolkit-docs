---
title:   Hands-on Labs Image Recognition
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   01/14/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# Hands-on Labs Image Recognition

Note that this tutorial requires the latest master version, or the upcoming CNTK 1.7 which will be released soon.
An intermediate binary download can be found in the [instructions for the KDD CNTK Hands-On Tutorial](./KDD-2016-Tutorial.md) that
this tutorial was originally designed for.

## Hands-On Lab: Image recognition with Convolutional Networks, Batch Normalization, and Residual Nets

This hands-on lab shows how to implement convolution-based image recognition with CNTK.
We will start with a common convolutional image-recognition architecture, add Batch Normalization,
and then extend it into a Residual Network (ResNet-20).

The techniques you will practice include:

* modifying a CNTK network definition to add a predefined operation (dropout) 
* creating user-defined functions to extract repetitive parts in a network into a reusable module
* implementing a custom network structures (ResNet skip connection)
* creating many layers at once using recursive loops
* parallel training
* convolutional networks
* batch normalization
<!-- File retired - We assume that you are familiar with basics of deep learning, and these specific concepts (if not,
you can catch up with [this two-page introduction](./Tutorial2/Tutorial2.md#going-deep-convolutional-neural-networks-cnns)):-->

### Prerequisites

We assume that you have already installed CNTK and can run the CNTK command.
This tutorial was held at KDD 2016 and requires a recent build,
please [see here](./Setup-CNTK-on-your-machine.md) for setup instructions. You can just follow the instructions 
for downloading a binary install package from that page. For image related tasks you should do this on a machine with a capable CUDA-compatible GPU.

Next, please download a ZIP archive (about 12 MB): Click on [this link](https://github.com/Microsoft/CNTK/blob/fseide/kdd/Tutorials/CNTK_HandsOn_KDD2016.zip),
and then on the Download button.
The archive contains the files for this tutorial. Please the archive
and set your working directory to `ImageHandsOn`.
You will be working with the following files:

* [`ImageHandsOn.cntk`](https://github.com/Microsoft/CNTK/tree/release/latest/Tutorials/ImageHandsOn/ImageHandsOn.cntk): The CNTK configuration file we will introduce below and work with.
* [`cifar10.pretrained.cmf`](https://github.com/Microsoft/CNTK/tree/release/latest/Tutorials/ImageHandsOn/cifar10.pretrained.cmf): Resulting model of the configuration we will begin with.
* [`cifar10.ResNet.cmf`](https://github.com/Microsoft/CNTK/tree/release/latest/Tutorials/ImageHandsOn/cifar10.ResNet.cmf): Resulting model of the ResNet version that we will create below.

Lastly we must download and convert the CIFAR-10 data set. The conversion step will take about 10 minute. Please execute the following two Python scripts which you will also find in the working directory:

    wget -rc http://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz
    tar xvf www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz
    python CifarConverter.py cifar-10-batches-py

This converts the images into PNG files, 50000 for training and 10000 for testing, which will be placed into the follow two directories, respectively: `cifar-10-batches-py/data/train` and `cifar-10-batches-py/data/test`.

## Model Structure

We will start this tutorial with a simple convolutional model. It consists of 3 layers of 5x5 convolutions + non-linearity + 2x dimension reduction by 3x3 max-pooling, which are then followed by a dense hidden layer and a dense transform to form the input to a 10-way softmax classifier.

Or, as a CNTK network description. Please have a quick look and match it with the description above:

    featNorm = features - Constant (128)
    l1 = ConvolutionalLayer {32, (5:5), pad = true, activation = ReLU,
                             init = "gaussian", initValueScale = 0.0043} (featNorm)
    p1 = MaxPoolingLayer {(3:3), stride = (2:2)} (l1)
    l2 = ConvolutionalLayer {32, (5:5), pad = true, activation = ReLU,
                             init = "gaussian", initValueScale = 1.414} (p1)
    p2 = MaxPoolingLayer {(3:3), stride = (2:2)} (l2)
    l3 = ConvolutionalLayer {64, (5:5), pad = true, activation = ReLU,
                             init = "gaussian", initValueScale = 1.414} (p2)
    p3 = MaxPoolingLayer {(3:3), stride = (2:2)} (l3)
    d1 = DenseLayer {64, activation = ReLU, init = "gaussian", initValueScale = 12} (p3)
    z  = LinearLayer {10, init = "gaussian", initValueScale = 1.5} (d1)

You can find more information on these operators here: [`ConvolutionalLayer{}`](./BrainScript-Layers-Reference.md#convolutionallayer), [`MaxPoolingLayer{}`](./BrainScript-Layers-Reference.md#maxpoolinglayer-averagepoolinglayer), [`DenseLayer{}`](./BrainScript-Layers-Reference.md#denselayer-linearlayer),  [`LinearLayer{}`](./BrainScript-Layers-Reference.md#denselayer-linearlayer).

## CNTK Configuration

### Config File

To train and test a model in CNTK, we need to provide a configuration file that tells CNTK what operations you want to run (`command` variable), and a parameter section for every command.

For the training command, CNTK needs to be told:

* how to read the data (`reader` section) 
* the model function and its inputs and outputs in the computation graph (`BrainScriptNetworkBuilder` section)
* hyper-parameters for the learner (`SGD` section)

For the evaluation command, CNTK needs to know:

* how to read the test data (`reader` section)
* which metrics to evaluate (`evalNodeNames` parameter) 

The following is the configuration file we will start with. As you see, a CNTK configuration file is a text file consisting of definitions of parameters, which are organized in a hierarchy of records. You can also see how CNTK supports basic parameter replacement using the `$parameterName$` syntax. The actual file contains just a few more parameters than mentioned above, but please scan it and locate the configuration items just mentioned:

```
# CNTK Configuration File for training a simple CIFAR-10 convnet.
# During the hands-on tutorial, this will be fleshed out into a ResNet-20 model.

command = TrainConvNet:Eval

makeMode = false ; traceLevel = 0 ; deviceId = "auto"

rootDir = "." ; dataDir  = "$rootDir$" ; modelDir = "$rootDir$/Models"

modelPath = "$modelDir$/cifar10.cmf"

# Training action for a convolutional network
TrainConvNet = {
    action = "train"

    BrainScriptNetworkBuilder = {
        imageShape = 32:32:3
        labelDim = 10

        model (features) = {
            featNorm = features - Constant (128)
            l1 = ConvolutionalLayer {32, (5:5), pad=true, activation=ReLU,
                                     init="gaussian", initValueScale=0.0043} (featNorm)
            p1 = MaxPoolingLayer {(3:3), stride=(2:2)} (l1)
            l2 = ConvolutionalLayer {32, (5:5), pad=true, activation=ReLU,
                                     init="gaussian", initValueScale=1.414} (p1)
            p2 = MaxPoolingLayer {(3:3), stride=(2:2)} (l2)
            l3 = ConvolutionalLayer {64, (5:5), pad=true, activation=ReLU,
                                     init="gaussian", initValueScale=1.414} (p2)
            p3 = MaxPoolingLayer {(3:3), stride=(2:2)} (l3)
            d1 = DenseLayer {64, activation=ReLU, init="gaussian", initValueScale=12} (p3)
            z  = LinearLayer {10, init="gaussian", initValueScale=1.5} (d1)
        }.z

        # inputs
        features = Input {imageShape}
        labels   = Input {labelDim}

        # apply model to features
        z = model (features)

        # connect to system
        ce       = CrossEntropyWithSoftmax (labels, z)
        errs     = ErrorPrediction         (labels, z)

        featureNodes    = (features)
        labelNodes      = (labels)
        criterionNodes  = (ce)
        evaluationNodes = (errs)
        outputNodes     = (z)
    }

    SGD = {
        epochSize = 50000

        maxEpochs = 30 ; minibatchSize = 64
        learningRatesPerSample = 0.00015625*10:0.000046875*10:0.000015625
        momentumAsTimeConstant = 600*20:6400
        L2RegWeight = 0.03

        firstMBsToShowResult = 10 ; numMBsToShowResult = 100
    }

    reader = {
        verbosity = 0 ; randomize = true
        deserializers = ({
            type = "ImageDeserializer" ; module = "ImageReader"
            file = "$dataDir$/cifar-10-batches-py/train_map.txt"
            input = {
                features = { transforms = (
                    { type = "Crop" ; cropType = "RandomSide" ; sideRatio = 0.8 ; jitterType = "UniRatio" } :
                    { type = "Scale" ; width = 32 ; height = 32 ; channels = 3 ; interpolations = "linear" } :
                    { type = "Transpose" }
                )}
                labels = { labelDim = 10 }
            }
        })
    }
}

# Eval action
Eval = {
    action = "eval"
    minibatchSize = 16
    evalNodeNames = errs
    reader = {
        verbosity = 0 ; randomize = true
        deserializers = ({
            type = "ImageDeserializer" ; module = "ImageReader"
            file = "$dataDir$/cifar-10-batches-py/test_map.txt"
            input = {
                features = { transforms = (
                   { type = "Scale" ; width = 32 ; height = 32 ; channels = 3 ; interpolations = "linear" } :
                   { type = "Transpose" }
                )}
                labels = { labelDim = 10 }
            }
        })
    }
}
```

### Data and Data Reading

After you have downloaded the CIFAR-10 data and run the `CifarConverter.py` script as requested
at the beginning of this tutorial,
you will find a directory named `cifar-10-batches-py/data`, which contains two subdirectories, `train` and `test`, full of PNG files.
The CNTK [`ImageDeserializer`](./BrainScript-and-Python---Understanding-and-Extending-Readers.md#deserializers) consumes standard image formats.

You will also find two files `train_map.txt` and  `test_map.txt`. Looking at the latter,

    % more cifar-10-batches-py/test_map.txt
    cifar-10-batches-py/data/test/00000.png 3
    cifar-10-batches-py/data/test/00001.png 8
    cifar-10-batches-py/data/test/00002.png 8
    ...

Both files consist of two columns, where the first contains the path to the image file, and the second
the class label, as a numeric index. These columns correspond to the reader inputs `features` and `labels`
which were defined as:

     features = { transforms = (
         { type = "Crop" ; cropType = "RandomSide" ; sideRatio = 0.8 ; jitterType = "UniRatio" } :
         { type = "Scale" ; width = 32 ; height = 32 ; channels = 3 ; interpolations = "linear" } :
         { type = "Transpose" }
     )}
     labels = { labelDim = 10 }

The additional `transforms` section tells the `ImageDeserializer` to apply a sequence of (common) transforms
to the images as they are being read.
For more information, [see here](./BrainScript-and-Python---Understanding-and-Extending-Readers.md#deserializers).

### Running it

You can find the above configuration file under the name `ImageHandsOn.cntk` in the working folder.
To run it,
please execute the above configuration by this command:

    cntk  configFile=ImageHandsOn.cntk

Your screen will come alive with a flurry of log messages (CNTK can be talkative at times),
but if everything went right, you will soon see this:

    Training 116906 parameters in 10 out of 10 parameter tensors and 28 nodes with gradient

followed by output like this:

    Finished Epoch[ 1 of 10]: [Training] ce = 1.66950797 * 50000; errs = 61.228% * 50000
    Finished Epoch[ 2 of 10]: [Training] ce = 1.32699016 * 50000; errs = 47.394% * 50000
    Finished Epoch[ 3 of 10]: [Training] ce = 1.17140398 * 50000; errs = 41.168% * 50000

This tells you that it is learning. Each Epoch represents one pass through the 50000 training images.
It also tells you that after the second epoch, the training criterion, which the configuration named `ce`, has reached 1.33 as measured on the 50000 samples of this epoch, and that the error rate is 47% on those same 50000 training samples.

Note CPU-only machines are about 20 times slower. It will take many minutes until you even see the first log output. To make sure the system is progressing, you can enable tracing to see partial results, which should appear reasonably quickly:

    cntk  configFile=ImageHandsOn.cntk  traceLevel=1

    Epoch[ 1 of 10]-Minibatch[-498-   1, 0.13%]: ce = 2.30260658 * 64; errs = 90.625% * 64
    ...
    Epoch[ 1 of 10]-Minibatch[   1- 100, 12.80%]: ce = 2.10434176 * 5760; errs = 78.472% * 5760
    Epoch[ 1 of 10]-Minibatch[ 101- 200, 25.60%]: ce = 1.82372971 * 6400; errs = 68.172% * 6400
    Epoch[ 1 of 10]-Minibatch[ 201- 300, 38.40%]: ce = 1.69708496 * 6400; errs = 62.469% * 6400

When training has completed (which takes about 3 minutes on a Surface Book and on a desktop machine with a Titan-X GPU),
the final message will be similar to this:

    Finished Epoch[10 of 10]: [Training] ce = 0.74679766 * 50000; errs = 25.486% * 50000

which shows that the network successfully reduced the `ce` loss, and reached a 25.5% classification
error on the training set. Because our `command` variable specifies a second command `Eval`, CNTK will then
proceed with that action. It measures the classification error rate on the 10000 images of the testing set.

    Final Results: Minibatch[1-625]: errs = 24.180% * 10000

The testing error rate is close to the training. Since CIFAR-10 is a rather small data set,
this is an indicator that our model has not yet converged fully (and in fact, running it for 30 epochs
will get you to about 20%).

If you do not want to wait until this finishes, you can run an intermediate model, e.g.

    cntk  configFile=ImageHandsOn.cntk  command=Eval  modelPath=Models/cifar10.cmf.5
    Final Results: Minibatch[1-625]: errs = 31.710% * 10000

or run our pre-trained model as well:

    cntk  configFile=ImageHandsOn.cntk  command=Eval  modelPath=cifar10.pretrained.cmf
    Final Results: Minibatch[1-625]: errs = 24.180% * 10000

## Modifying the Model

In the following, you will be given tasks to practice modifying CNTK configurations.
The solutions are given at the end of this document... but please try without!

### Task 1: Adding Dropout

A common technique to improve generalizability of models is dropout. To add dropout to a CNTK model, you need

* add a call to the CNTK function [`Dropout()`](./Dropout.md) where you want to insert the dropout operation
* add a parameter `dropoutRate` to the `SGD` section called to define the dropout probability

In this specific task, please specify no dropout for the first 1 epoch, followed by a dropout rate of 50%.
Please have a look at the [`Dropout()`](./Dropout.md) documentation to see how to do that.

If everything went well, you will observe no change for the first 1 epoch, but a much lesser improvement
of `ce` once dropout kicks in with the second epoch. This is expected. (For this specific configuration, recognition accuracy does not improve, actually.) The final result when training only 10 epochs is about 32%.
10 epochs are not enough for Dropout.

Please see the solution [here](#solution-1-adding-dropout).

### Task 2: Simplify Model Definition by Extracting Repetitive Parts into a Function 

In the example, the sequence (Convolution >> ReLU >> Pooling) is repeated three times.
Your task is to write a BrainScript function that groups these three operations into a reusable module.
Note that all three uses  of this sequence use different parameters (output dimensions, initialization weight).
Thus, the function you write should take these two as parameters, in addition to the input data.
For example, it could be defined as

	MyLayer (x, depth, initValueScale)

When you run this, you would expect the resulting `ce` and `errs` values to be the same.
However, if you run on the GPU, non-determinism in cuDNN's back-propagation implementation
will cause minor variations. 

Please see the solution [here](#solution-2-simplify-model-definition-by-extracting-repetitive-parts-into-a-function).

### Task 3: Adding BatchNormalization

(This Task requires a GPU, since CNTK's implementation of batch normalization is based on cuDNN.)

Batch normalization is a popular technique to speed up and improve convergence.
In CNTK, batch normalization is implemented as [`BatchNormalizationLayer{}`](./BrainScript-Layers-Reference.md#batchnormalizationlayer-layernormalizationlayer-stabilizerlayer).

The spatial form (where all pixel positions are normalized with shared parameters)
is invoked by an optional parameter: `BatchNormalizationLayer{spatialRank=2}`.

Please add batch normalization to all three convolution layers and between the two dense layers.
Note that batch normalization should be inserted right before the non-linearity.
Hence, you must remove the `activation` parameter and instead insert explicit
calls to the CNTK function [`ReLU()`](./BrainScript-Activation-Functions.md).

Further, batch normalization changes convergence speed.
So let us increase the learning rates
for the first 7 epochs 3-fold, and disable momentum and L2 regularization by
setting their parameters to 0.

When running, you will see additional learnable parameters listed in the training log.
The final result will be around 28%, which is 4 points better than without batch normalization
after the same number of iterations.
Convergence speeds up indeed.

Please see the solution [here](#solution-3-adding-batchnormalization).

### Task 4: Convert to Residual Net

The above configuration is a "toy" example to get your hands dirty with running and
modifying CNTK configurations, and we intentionally did not run to full convergence to keep the turnaround
times for you low.
So let us now move forward to a more real configuration--a Residual Net.
The Residual Net (https://arxiv.org/pdf/1512.03385v1.pdf) is a modified
deep network structure where layers, instead of learning
a mapping from input to output, learn a correction term.

(This Task also requires a GPU for the batch normalization operation, although if you have *lots* of time,
you can try running it on a CPU by editing out the calls batch normalization, at some loss of accuracy.)

To get started, please modify the previous configuration. First, please replace the model function `model(features)` with this one:

```
        MySubSampleBN (x, depth, stride) =
        {
            s = Splice ((MaxPoolingLayer {(1:1), stride = (stride:stride)} (x) : ConstantTensor (0, (1:1:depth/stride))), axis = 3)  # sub-sample and pad: [W x H x depth/2] --> [W/2 x H/2 x depth]
            b = BatchNormalizationLayer {spatialRank = 2, normalizationTimeConstant = 4096} (s)
        }.b
        MyConvBN (x, depth, initValueScale, stride) =
        {
            c = ConvolutionalLayer {depth, (3:3), pad = true, stride = (stride:stride), bias = false,
                                    init = "gaussian", initValueScale = initValueScale} (x)
            b = BatchNormalizationLayer {spatialRank = 2, normalizationTimeConstant = 4096} (c)
        }.b
        ResNetNode (x, depth) =
        {
            c1 = MyConvBN (x,  depth, 7.07, 1)
            r1 = ReLU (c1)
            c2 = MyConvBN (r1, depth, 7.07, 1)
            r  = ReLU (c2)
        }.r
        ResNetIncNode (x, depth) =
        {
            c1 = MyConvBN (x,  depth, 7.07, 2)  # note the 2
            r1 = ReLU (c1)
            c2 = MyConvBN (r1, depth, 7.07, 1)
            r  = ReLU (c2)
        }.r
        model (features) =
        {
            conv1 = ReLU (MyConvBN (features, 16, 0.26, 1))
            rn1   = ResNetNode (ResNetNode (ResNetNode (conv1, 16), 16), 16)

            rn2_1 = ResNetIncNode (rn1, 32)
            rn2   = ResNetNode (ResNetNode (rn2_1, 32), 32)

            rn3_1 = ResNetIncNode (rn2, 64)
            rn3   = ResNetNode (ResNetNode (rn3_1, 64), 64)

            pool = AveragePoolingLayer {(8:8)} (rn3)

            z = LinearLayer {labelDim, init = "gaussian", initValueScale = 0.4} (pool)
        }.z
```

and change the SGD configuration to:

    SGD = {
        epochSize = 50000

        maxEpochs = 160 ; minibatchSize = 128
        learningRatesPerSample = 0.0078125*80:0.00078125*40:0.000078125
        momentumAsTimeConstant = 1200
        L2RegWeight = 0.0001

        firstMBsToShowResult = 10 ; numMBsToShowResult = 500
    }

Your task is to modify `ResNetNode()` and `ResNetNodeInc()` so that they implement the structure
laid out in the following award-worthy ASCII art:

                ResNetNode                   ResNetNodeInc

                    |                              |
             +------+------+             +---------+----------+
             |             |             |                    |
             V             |             V                    V
        +----------+       |      +--------------+   +----------------+
        | Conv, BN |       |      | Conv x 2, BN |   | SubSample, BN  |
        +----------+       |      +--------------+   +----------------+
             |             |             |                    |
             V             |             V                    |
         +-------+         |         +-------+                |
         | ReLU  |         |         | ReLU  |                |
         +-------+         |         +-------+                |
             |             |             |                    |
             V             |             V                    |
        +----------+       |        +----------+              |
        | Conv, BN |       |        | Conv, BN |              |
        +----------+       |        +----------+              |
             |             |             |                    |
             |    +---+    |             |       +---+        |
             +--->| + |<---+             +------>+ + +<-------+
                  +---+                          +---+
                    |                              |
                    V                              V
                +-------+                      +-------+
                | ReLU  |                      | ReLU  |
                +-------+                      +-------+
                    |                              |
                    V                              V

Please confirm with the validation output in the log that you did it right.

This will take long to run to completion. The expected output will be similar to this:

    Finished Epoch[ 1 of 160]: [Training] ce = 1.57037109 * 50000; errs = 58.940% * 50000
    Finished Epoch[ 2 of 160]: [Training] ce = 1.06968234 * 50000; errs = 38.166% * 50000
    Finished Epoch[ 3 of 160]: [Training] ce = 0.85858969 * 50000; errs = 30.316% * 50000

whereas the incorrect model without the skip connections looks more like this:

    Finished Epoch[ 1 of 160]: [Training] ce = 1.72901219 * 50000; errs = 66.232% * 50000
    Finished Epoch[ 2 of 160]: [Training] ce = 1.30180430 * 50000; errs = 47.424% * 50000
    Finished Epoch[ 3 of 160]: [Training] ce = 1.04641961 * 50000; errs = 37.568% * 50000

Please see the solution [here](#solution-4-convert-to-residual-net).

### Task 5: Automatically Generating Many Layers

Lastly, the best-performing ResNet has 152 layers. Since it would be very tedious
and error prone to write 152 individual expressions, we will now modify the definition
to automatically generate a stack of `ResNetNode()`.

Your task is to write a function with this signature:

    ResNetNodeStack (x, depth, L)

where `L` denotes how many `ResNetNodes` should be stacked, so that we can
replace the expression for `rn1` above with a parameterized call:

    rn1   = ResNetNodeStack (conv1, 16, 3)  # 3 means 3 such nodes

and likewise for `rn2` and `rn3`.
The tools you will need are the *conditional* expression:

    z = if cond then x else y

and recursion.

This training will run for about half an our on a Titan-X.
If you did it right, early on the log will contain this message:

	Training 200410 parameters in 51 out of 51 parameter tensors and 112 nodes with gradient:

For reference, we include a pre-trained version of this model.
You can measure the error rate with this command:

	cntk  configFile=ImageHandsOn.ResNet.cntk  command=Eval

and should see a result like this one:

    Final Results: Minibatch[1-625]: errs = 8.400% * 10000; top5Errs = 0.290% * 10000

This error rate is very close to that reported in the original ResNet paper (https://arxiv.org/pdf/1512.03385v1.pdf, Table 6).

Please see the solution [here](#solution-5-automatically-generating-many-layers).

### Task 6: Parallel Training

Lastly, if you have multiple GPUs, CNTK allows you to parallelize the training using MPI (Message-Passing Interface). This model is too small to expect much speed-up without further tuning e.g. of minibatch sizes
(the current minibatch-size setting is too small to get full utilization of available GPU cores).
Nevertheless, let us go through the motions, so that you know how to do it once you move on to real-world workloads.

Please add the following line to the `SGD` block:

    SGD = {
        ...
        parallelTrain = {
            parallelizationMethod = "DataParallelSGD"
            parallelizationStartEpoch = 2
            distributedMBReading = true
            dataParallelSGD = { gradientBits = 1 }
        }
    }

and then execute this command:

    mpiexec -np 4 cntk  configFile=ImageHandsOn.cntk  stderr=Models/log  parallelTrain=true

## What's Next?

This tutorial has practiced to take an existing configuration, and modifying it in specific ways:

* adding a predefined operation (dropout)
* extracting repetitive parts into reusable modules (functions)
* refactoring (to insert batch normalization)
* custom network structures (ResNet skip connection)
* parameterizing repetitive structures using recursion

and we have seen how to speed up the training by parallelization.

So where do we go from here?
You might already have discovered that the pattern used in these examples, which we call *graph-building* style,
can be quite error prone. Spot the error?

    model (features) =
    {
        l1 = ConvolutionalLayer {32, (5:5), pad = true, activation = ReLU,
                                 init = "gaussian", initValueScale = 0.0043} (featNorm)
        p1 = MaxPoolingLayer {(3:3), stride = (2:2)} (l1)
        l2 = ConvolutionalLayer {64, (5:5), pad = true, activation = ReLU,
                                 init = "gaussian", initValueScale = 1.414} (p1)
        p2 = MaxPoolingLayer {(3:3), stride = (2:2)} (l1)
        d1 = DenseLayer {64, activation = ReLU, init = "gaussian", initValueScale = 12} (p2)
        z  = LinearLayer {10, init = "gaussian", initValueScale = 1.5} (d1)
    }.z

A way to avoid this error is to use *function composition*. The following is an alternative,
more concise way of writing the same:

    model = Sequential (
        ConvolutionalLayer {32, (5:5), pad = true, activation = ReLU,
                            init = "gaussian", initValueScale = 0.0043} :
        MaxPoolingLayer {(3:3), stride = (2:2)} :
        ConvolutionalLayer {64, (5:5), pad = true, activation = ReLU,
                            init = "gaussian", initValueScale = 1.414} :
        MaxPoolingLayer {(3:3), stride = (2:2)} :
        DenseLayer {64, activation = ReLU, init = "gaussian", initValueScale = 12} :
        LinearLayer {10, init = "gaussian", initValueScale = 1.5}
    )

This style will be introduced and used in the next hands-on tutorial,
[*Text Understanding with Recurrent Networks*](./Hands-On-Labs-Language-Understanding.md). 

## Solutions

### Solution 1: Adding Dropout

Modify the model definition as follows:

    p3 = MaxPoolingLayer {(3:3), stride = (2:2)} (l3)
    d1 = DenseLayer {64, activation = ReLU, init = "gaussian", initValueScale = 12} (p3)
    d1_d = Dropout (d1)    ##### added
    z  = LinearLayer {10, init = "gaussian", initValueScale = 1.5} (d1_d)  ##### d1 -> d1_d

and the SGD section:

    SGD = {
        ...
        dropoutRate = 0*5:0.5   ##### added
        ...
    }

### Solution 2: Simplify Model Definition by Extracting Repetitive Parts into a Function

Add a function definition as follows:

    MyLayer (x, depth, initValueScale) =
    {
        c = ConvolutionalLayer {depth, (5:5), pad = true, activation = ReLU,
                                init = "gaussian", initValueScale = initValueScale} (x)
        p = MaxPoolingLayer {(3:3), stride = (2:2)} (c)
    }.p

and update the model definition to use it

    featNorm = features - Constant (128)
    p1 = MyLayer (featNorm, 32, 0.0043)  ##### replaced
    p2 = MyLayer (p1,       32, 1.414)   ##### replaced
    p3 = MyLayer (p2,       64, 1.414)   ##### replaced
    d1 = DenseLayer {64, activation = ReLU, init = "gaussian", initValueScale = 12} (p3)

### Solution 3: Adding BatchNormalization

Modify `MyLayer()`:

    MyLayer (x, depth, initValueScale) =
    {
        c = ConvolutionalLayer {depth, (5:5), pad = true,  ##### no activation=ReLU
                                init = "gaussian", initValueScale = initValueScale} (x)
        b = BatchNormalizationLayer {spatialRank = 2} (c)
        r = ReLU (b)   ##### now called explicitly
        p = MaxPoolingLayer {(3:3), stride = (2:2)} (r)
    }.p

and use it. Also insert batch normalization before `z`: 

    d1 = DenseLayer {64, init = "gaussian", initValueScale = 12} (p3)
    d1_bnr = ReLU (BatchNormalizationLayer {} (d1))  ##### added BN and explicit ReLU
    d1_d = Dropout (d1_bnr)                          ##### d1 -> d1_bnr
    z  = LinearLayer {10, init = "gaussian", initValueScale = 1.5} (d1_d)

And update these parameters in the SGD section:

    SGD = {
        ....
        learningRatesPerSample = 0.00046875*7:0.00015625*10:0.000046875*10:0.000015625
        momentumAsTimeConstant = 0
        L2RegWeight = 0
        ...
    }

### Solution 4: Convert to Residual Net

The correct implementations for `ResNetNode()` and `ResNetNodeInc()` are:

        ResNetNode (x, depth) =
        {
            c1 = MyConvBN (x,  depth, 7.07, 1)
            r1 = ReLU (c1)
            c2 = MyConvBN (r1, depth, 7.07, 1)
            r  = ReLU (x + c2)   ##### added skip connection
        }.r
        ResNetIncNode (x, depth) =
        {
            c1 = MyConvBN (x,  depth, 7.07, 2)  # note the 2
            r1 = ReLU (c1)
            c2 = MyConvBN (r1, depth, 7.07, 1)

            xs = MySubSampleBN (x, depth, 2)

            r  = ReLU (xs + c2)   ##### added skip connection
        }.r

### Solution 5: Automatically Generating Many Layers

This is the implementation:

        ResNetNodeStack (x, depth, L) =
        {
            r = if L == 0
                then x
                else ResNetNode (ResNetNodeStack (x, depth, L-1), depth)
        }.r

or, shorter:

        ResNetNodeStack (x, depth, L) =
            if L == 0
            then x
            else ResNetNode (ResNetNodeStack (x, depth, L-1), depth)

You also need to modify your model function:

            conv1 = ReLU (MyConvBN (features, 16, 0.26, 1))
            rn1   = ResNetNodeStack (conv1, 16, 3)  ##### replaced

            rn2_1 = ResNetIncNode (rn1, 32)
            rn2   = ResNetNodeStack (rn2_1, 32, 2)  ##### replaced

            rn3_1 = ResNetIncNode (rn2, 64)
            rn3   = ResNetNodeStack (rn3_1, 64, 2)  ##### replaced
