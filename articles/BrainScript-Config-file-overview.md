---
title:   BrainScript config file overview
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   03/22/2017
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   brainscript
---

# BrainScript config file overview

To run CNTK you use a command line similar to `cntk configFile=yourExp.cntk` where yourExp.cntk is a CNTK configuration file, which typically contains several command blocks. A command block is a top level block of the configuration. Each command block must specify what action to be carried out with related information. To illustrate configuration and command blocks, we use a simple example below (taken from the [MNIST example](Examples.md)).

    rootDir = ".."   # often, this is overwritten on the command line

    configDir = "$rootDir$/Config"
    dataDir   = "$rootDir$/Data"
    outputDir = "$rootDir$/Output"
    modelDir  = "$outputDir$/Models"

    command = mnistTrain

    mnistTrain = [
        action = "train"

        # network definition
        BrainScriptNetworkBuilder = (new ComputationNetwork
            include "$ConfigDir$/01_OneHidden.bs"
        )

        # SGD learner configuration
        SGD = [
            modelPath = "$ModelDir$/01_OneHidden_Model.dnn"
            epochSize = 60000
            minibatchSize = 32
            learningRatesPerMB = 0.1
            maxEpochs = 30
        ]

        # reader configuration    
        reader = [
            readerType = "CNTKTextFormatReader"
            file = "$DataDir$/Train-28x28_cntk_text.txt"
            input = [
                features = [
                    dim = 784
                    format = "dense"
                ]
                labels = [
                    dim = 10
                    format = "dense"
                ]
            ]
        ]
    ]

In this example, you can notice that all configuration values are specified as a name-value pair. A value can be a numeric, a string, a list, or even a block of configurations. You can specify variables, such as `DataDir` in this example, and refer back to them later in the script by using the notation `$DataDir$`.

The top-level configuration parameter `command` determines what command blocks are to be executed and in what order they are executed if more than one command block is specified. In this example, the `mnistTrain` command block will be executed. This command block specifies the action to execute, which is `train` in this case. There are often three parameter blocks associated with the train action: 
* a network builder block, which specifies how to build a network from scratch and how to load a model from an existing model file, 
* a learner block, which specifies what training algorithm to use, 
* a reader block, which specifies where and how to load features and labels. 

In this specific example, the network builder indicated by the [BrainScriptNetworkBuilder](./BrainScript-Network-Builder.md) block is used to define the network, the stochastic gradient descent learning algorithm as indicated
by the [SGD block](./BrainScript-SGD-block.md) is used to train the model, and the [CNTK Text Format Reader](./BrainScript-CNTKTextFormat-Reader.md) is used to load the features and labels from files in CNTK Text format. Note that readers are implemented as separate DLLs, and the name of the reader is also the name of the DLL file that will be loaded to read data.

The most frequently used configuration blocks are:
* Network Builders
  * [SimpleNetworkBuilder](./Simple-Network-Builder.md) - creates one of the predefined networks with limited customization.
  * [BrainScriptNetworkBuilder](./BrainScript-Network-Builder.md) - creates a network defined using CNTK's network description language ([BrainScript](./BrainScript-Basic-concepts.md)). It provides full flexibility in designing your own network operations and structure.
* Learners
  * [SGD](./BrainScript-SGD-block.md) - uses the stochastic gradient descent algorithm to train the model. It is the desired trainer for most applications.
* Data Readers
  * [CNTK Text Format Reader](./BrainScript-CNTKTextFormat-Reader.md) - reads the text-based CNTK format, which supports multiple inputs combined in the same file.
  * [UCI Fast Reader (deprecated)](./BrainScript-UCI-Fast-Reader.md) - reads the text-based UCI format, which contains labels and features combined in one file.
  * [HTKMLF Reader](./BrainScript-HTKMLF-Reader.md) - reads the HTK/MLF format files, often used in speech recognition applications.
  * [LM Sequence Reader](./BrainScript-LM-Sequence-Reader.md) - reads text-based files that contain word sequences, for predicting word sequences. This is often used in language modeling.
  * [LU Sequence Reader](./BrainScript-LU-Sequence-Reader.md) - reads text-based files that contain word sequences and their labels. This is often used for language understanding.

In the following subsections, we will describe CNTK configuration and the above blocks in detail. You can also jump to the individual blocks using the links in the text above.

**See also**
* [Command line parsing rules](./BrainScript-Command-line-parsing-rules.md)
