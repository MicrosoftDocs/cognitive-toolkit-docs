This page describes how to expose a trained model's hidden layer's values.

### Overview
A CNTK model is built on interconnected layers. Some of these layers can be evaluated using the `EvalDll` because they are tagged as being 'output' layers. In order to expose other layers through the `EvalDll`, these layers must be tagged as output layers by adding them to the `outputNodes` property.

For example, the `01_OneHidden_ndl_deprecated.cntk` configuration file refers to the `01_OneHidden.ndl` file for the network definition. In this network description file, we have two layers defined:

    h1 = DNNSigmoidLayer (featDim, hiddenDim, featScaled, 1)  
    ol = DNNLayer (hiddenDim, labelDim, h1, 1)  

But only one layer is marked as an output:

    outputNodes = (ol)

Thus, the `EvalDll` will only return values pertaining to the `ol` layer during evaluation.

In order to be able to evaluate the `h1` hidden layer, we need to expose it first as an output node. There are three possible ways:

### 1. Training model with hidden layers exposed
To output the `h1` layer, just add it as an output in the network description (`01_OneHidden.bs` file) when training it, and that layer would be available for reading during evaluation:

    outputNodes = (h1:ol)

**However this implies the model would need to be (re)trained with this configuration.**

### 2. Modifying an already trained model
Models can be modified on the fly when being loaded using BrainScript expressions.
This will be documented in a future update of this documentation.

### 3. Changing output-node set of an already trained model while loading it for evaluation using the `EvalDll`/`EvalDllWrapper` modules
If a trained will be evaluated using the `EvalDll`/`EvalDllWrapper` modules, you can add the `outputNodeNames` property with a colon separated list of nodes to the network definition:

    outputNodeNames = "h1.z:ol.z"

When loading the network, the Eval engine will recognize the `outputNodeNames` property and replace the model's output nodes with the list of nodes specified in the `outputNodeNames` property.

Looking at the code inside the `CPPEvalClient` example project, shows the (uncommented) line specifying the `outputNodeNames` property:

    networkConfiguration += "outputNodeNames=\"h1.z:ol.z\"\n";
    networkConfiguration += "modelPath=\"" + modelFilePath + "\"";
    model->CreateNetwork(networkConfiguration);

Running the program shows the corresponding output for the `h1.z` layer.
