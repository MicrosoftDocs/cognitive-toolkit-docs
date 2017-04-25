---
title:   CNTK usage overview
author:    chrisbasoglu
date:    04/02/2017
ms.author:   cbasoglu
ms.date:   04/02/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK usage overview

To use CNTK you need to either download the executable binaries or download the source code and compile it on your machine ([details](Setup CNTK on your machine.md)).
There are three main tasks (or actions) that are supported by CNTK:
* **Train** - Define a network and train it to produce a trained model using training data
* **Evaluate** - Test a trained model to assess its performance using test data
* **Deploy** - Use a trained model, e.g. in your own solution, to classify new instances

A brief overview for each of these tasks is given below and pointers to a more detailed description are provided.
In addition there are other tasks that CNTK supports such as **edit** existing models and **write** node outputs to a file. A description of these is provided in the Advanced Topics section on the [Top-level commands](./Top-level-commands.md) page.

## Training a model using CNTK

Training a neural network with CNTK involves three components that must be configured:

* **network**: the neural network, including its structure/formula, model parameters. Training criteria and evaluation metric are also included here.
* **reader**: how training data is read
* **SGD**: the hyper-parameters of the stochastic-gradient process

You need to provide this information through a configuration file as the first argument when calling the CNTK executable. The configuration file uses a specific syntax. Please see [Config file overview](./BrainScript-Config-file-overview.md) for details on config files.

In the following we use the CNTK configuration and results from the MNIST example, in particular the configuration '01_OneHidden_ndl_deprecated.cntk' (see [Image/GettingStarted](https://github.com/Microsoft/CNTK/blob/master/Examples/Image/GettingStarted/README.md) and [01_OneHidden.cntk](https://github.com/Microsoft/CNTK/blob/master/Examples/Image/GettingStarted/01_OneHidden.cntk) for full details).

The CNTK command line for this example is `cntk  configFile=01_OneHidden_ndl_deprecated.cntk`. The following snippet provides an overview of the config file contents that are relevant for training.

    modelDir = "$OutputDir$/Models"
    deviceId = 0
    command = MNISTtrain

    modelPath = "$modelDir$/01_OneHidden"
    
    MNISTtrain = [
        action = "train"
 
        # network definition   
        BrainScriptNetworkBuilder = (new ComputationNetwork
            include "$ConfigDir$/01_OneHidden.bs"
        )
 
        # learner configuration       
        SGD = [
            ...
        ]
 
        # reader configuration   
        reader = [
            readerType = "CNTKTextFormatReader"
            file = "$DataDir$/Train-28x28_cntk_text.txt"
            ...
        ]    
    ]

The above code snippet defines a command called `MNISTtrain` with `action = "train"`. Other supported actions are for example `test` or `write`. The `deviceId` parameter specifies whether to use CPU or GPU. When set to `"auto"`, CNTK will pick the best available device. Set it to `-1` to use the CPU or to a value >=0 to use a specific GPU. The `modelPath` defines where to store the intermediate and final trained models. In this example it uses the `ModelDir` variable defined at the beginning of the configuration file.

The three main configuration blocks for training define the network itself and the parameters for the training algorithm and the data reader.

* Network builder - here you define the topology and the details of the network such as the size and number of layers and the type of nodes. You can use the [Simple Network Builder](./Simple-Network-Builder.md) for standard models or the [BrainScript Network Builder](./BrainScript-Network-Builder.md) for custom ones. Please refer to the corresponding Wiki pages for details.
* SGD - this block lets you parameterize the training algorithm (stochastic gradient descent). Configurable options include momentum, adaptive learning rate, adaptive minibatch size, parallel training. See [SGD block](./BrainScript-SGD-block.md) for more details.
* reader - the reader block defines which reader to use and where the corresponding input files are. CNTK provides several data readers for different formats and tasks (see [Reader block](./BrainScript-Reader-block.md)).

Finally, the line `command = MNISTtrain` specifies which of the defined tasks to execute. To execute several tasks consecutively, e.g. training and evaluation, simply add more tasks to the command separated by a colon: `command = "MNISTtrain:MNISTtest"`.

## Evaluating a trained model

To evaluate a trained model's accuracy, you use the `eval` or `test` command (see also [Train, Test, Eval](./BrainScript-Train-Test-Eval.md) for full details). The corresponding configuration in the MNIST [01_OneHidden.cntk](https://github.com/Microsoft/CNTK/blob/master/Examples/Image/GettingStarted/01_OneHidden.cntk) example looks as follows.
```
testNetwork = {
    action = "test"
    minibatchSize = 1024    # reduce this if you run out of memory

    reader = {
        readerType = "CNTKTextFormatReader"
        file = "$DataDir$/Test-28x28_cntk_text.txt"
        input = {
            features = { dim = 784 ; format = "dense" }
            labels =   { dim = 10  ; format = "dense" }
        }
    }
}
```

The `MNISTtest` block uses `action = "test"`. For the `test` action you need to define a model that should be used for testing using the `modelPath` parameter. In this example the `modelPath` is not defined inside the `MNISTtest` block but on the top level (see training part above) and is used by both the `train` and `test` actions. Inside the `reader` block you specify the data file that should be used for testing, `Test-28x28.txt` in the example. Finally, you have to set `command = MNISTtest` and run `cntk  configFile=01_OneHidden_ndl_deprecated.cntk` to execute the testing. The result on the command line is: 

    Final Results: Minibatch[1-625]: errs = 2.39% * 10000; ce = 0.076812531 * 10000; Perplexity = 1.0798396
    COMPLETED!

## Using a trained model in your own code

Once you have trained a model, you need the functionality to evaluate the model in your target environment. CNTK provides multiple ways to serve your models in different scenarios. You can use a trained model from C++, Python, C# or other .NET languages. You can run evaluation on your machine or in Azure. The section [Evaluating CNTK Models](./CNTK-Evaluation-Overview.md) (see sidebar) has many details including evaluation using C++/Python/C#/Azure.

**Next steps**
* [Model Evaluation Detailed](./CNTK-Evaluation-Overview.md)
* [Examples](./Examples.md)
* [Config file overview](./BrainScript-Config-file-overview.md)
* [Command line parsing rules](./BrainScript-Command-line-parsing-rules.md)
