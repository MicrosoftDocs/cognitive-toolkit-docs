---
title:   BrainScript Network Builder
author:    chrisbasoglu
date:    09/13/2016
ms.author:   cbasoglu
ms.date:   09/13/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# BrainScript Network Builder

Custom networks are described in CNTK's custom network description language "BrainScript." To define a custom network, include a section named `BrainScriptNetworkBuilder` in your training configuration. Detailed description on the network description language can be found on the [Basic Concepts](./BrainScript-Basic-Concepts.md) page and the corresponding sub-pages.

There are two forms of using the BrainScript network builder, one using parentheses `(...)`, and a short-hand form using curly braces `{...}`. To describe your network in an external file, specify a block similar to this:

    BrainScriptNetworkBuilder = (new ComputationNetwork {
        include "yourNetwork.bs"
    })

where `yourNetwork.bs` contains the network described using BrainScript. The file `yourNetwork.bs` is look for first in the same directory as the config file, and if not found, in the directory of the CNTK executable. Both absolute and relative pathnames are accepted here. E.g., `bs/yourNetwork.bs` means a file located in a directory `bs` next to your config file (or alternatively a directory `bs` inside the CNTK executable directory).

Note: Until CNTK 1.6, BrainScript used brackets `[...]` instead of curly braces `{...}`. Brackets are still accepted but deprecated.

Alternatively, you can define your network inline, directly inside the config file. This can simplify configuration if you don't plan to share the same brain script across multiple configurations. Use this form:

    BrainScriptNetworkBuilder = {
        # insert network description here
    }

So what does the BrainScript code look like that goes into the brackets? To find out, jump right ahead to [BrainScript Basic Concepts](./BrainScript-Basic-Concepts.md).

Or stay on this page and read on about some less frequently needed details for you.

The `{...}` form above is really just a short-hand for this:

    BrainScriptNetworkBuilder = (new ComputationNetwork {
        # insert network description here
    })

Lastly, as an advanced use, the `(...)` form is not limited to use `new`. Rather, any BrainScript expression that evaluates to an object of `ComputationNetwork` is allowed inside the parentheses. For example:

    BrainScriptNetworkBuilder = ({
        include "myNetworks.bs"
        network = CreateMyNetworkOfType42()
    }.network)

This is an advanced use that also sometimes occurs in the context of model-editing.

Next: [BrainScript Basic Concepts](./BrainScript-Basic-Concepts.md).

## Legacy `NDLNetworkBuilder`

In older versions of CNTK, the network builder was called `NDLNetworkBuilder`. Its definition language is a subset of BrainScript. The old parser was less capable, but also more forgiving. There are also other small differences.

`NDLNetworkBuilder` is now deprecated, but due to the similarity, it is not difficult to upgrade to `BrainScriptNetworkBuilder`. The following is a guide on how convert `NDLNetworkBuilder` network descriptions to `BrainScriptNetworkBuilder`'s.

### Updating from `NDLNetworkBuilder` to `BrainScriptNetworkBuilder`

Converting an existing network definition for the `NDLNetworkBuilder` to `BrainScriptNetworkBuilder` is simple in most cases. The main changes are the surrounding syntax. The core network description itself is largely upwards compatible and likely identical or near-identical if you don't take advantage of the new language features.

To convert your descriptions, you must switch the network builder, adapt w.r.t. outer syntax, and possibly make minor adaptations to your network code itself.

**Step 1. Switching the network builder**. Replace the `NDLNetworkBuilder` with the corresponding `BrainScriptNetworkBuilder` block in the CNTK config file. If your network description is in a separate file:

    # change from:
    NDLNetworkBuilder = [
        ndlMacros = "shared.ndl"   # (if any)
        networkDescription = "yourNetwork.ndl"
    ]
    # ...to:
    BrainScriptNetworkBuilder = (new ComputationNetwork {
        include "shared.bs"        # (if any)
        include "yourNetwork.bs"
    })

(The change of filename extension is not strictly necessary but recommended.)

If your network description is in the `.cntk` config file itself: 

    # change from:
    NDLNetworkBuilder = [
        # macros
        load = [
            SigmoidNetwork (x, W, b) = Sigmoid (Plus (Times (W, x), b))
        ]
        # network description
        run = [
            feat = Input (13)
            ...
            ce = CrossEntropyWithSoftmax (labels, z, tag="criterion")
        ]
    ]
    # ...to:
    BrainScriptNetworkBuilder = {
        # macros are just defined inline
        SigmoidNetwork (x, W, b) = Sigmoid (Plus (Times (W, x), b))  # or: Sigmoid (W * x + b)
        # network description
        feat = Input {13}
        ...
        ce = CrossEntropyWithSoftmax (labels, z, tag="criterion")
    }

**Step 2. Remove `load` and `run` blocks**. With `BrainScriptNetworkBuilder`, macro/function definitions and main code are combined. The `load` and `run` blocks must simply be removed. For example, this:

    load = ndlMnistMacros
    run = DNN
    ndlMnistMacros = [
        featDim = 784
        ...
        labels = InputValue(labelDim)
    ]
    DNN = [
        hiddenDim = 200
        ...
        outputNodes = (ol)
    ]

simply becomes:

    featDim = 784
    ...
    labels = InputValue(labelDim)
    hiddenDim = 200
    ...
    outputNodes = (ol)

You may have used the `run` variable to select one of multiple configurations with an external variable, e.g.:

    NDLNetworkBuilder = [
        run = $whichModel$   # outside parameter selects model, must be either "model1" or "model2"
        model1 = [ ... (MODEL 1 DEFINITION) ]
        model2 = [ ... (MODEL 1 DEFINITION) ]
    ]

This pattern was mostly necessary because NDL did not have conditional expressions. In BrainScript, this would now be written with an `if` expression:

    BrainScriptNetworkBuilder = (new ComputationNetwork
        if      "$whichModel$" == "model1" then { ... (MODEL 1 DEFINITION) }
        else if "$whichModel$" == "model2" then { ... (MODEL 2 DEFINITION) }
        else Fail ("Invalid model selector value '$whichModel$'")
    )

However, often, the selected models are very similar, so a better way would be to merge their descriptions and instead use conditionals inside only for where they differ. Here is an example where a parameter is used to choose between a unidirectional and a bidirectional LSTM:

    encoderFunction =
        if useBidirectionalEncoder
        then BS.RNNs.RecurrentBirectionalLSTMPStack
        else BS.RNNs.RecurrentLSTMPStack
    encoder = encoderFunction (encoderDims, inputEmbedded, inputDim=inputEmbeddingDim)

**Step 3. Adjust your network description**. Regarding the network description (formulas) itself,
BrainScript is largely upwards compatible with NDL. These are the main differences:

* The return value of macros (functions) is no longer the last variable defined in them, but the entire set of variables. You must explicitly select the output value at the end. For example:

        # NDL:  
        f(x) = [  
            x2 = Times (x, x)  
            y = Plus (x2, Constant (1))  
        ]  # <-- return value defaults to last entry, i.e. y  
        # BrainScript:
        f(x) = {
            x2 = x*x  
            y = x2 + 1  
        }.y  # <-- return value y must be explicitly dereferenced

    Without this change, the function return value would be the entire record, and the typical error you will get is that a `ComputationNode` was expected where a `ComputationNetwork` was found.

* BrainScript does not allow functions with variable numbers of parameters. This matters primarily for the `Parameter()` function: A vector parameter can no longer be written as `Parameter(N)`, it now has to be explicitly written as a tensor `ParameterTensor{N}` or a 1-column matrix `Parameter(N, 1)`. Without this change, you will get an error about mismatching number of positional parameters. This notation also works with NDL, so you can make this change first and test it with *NDL* before converting. This is also a good opportunity to rename any uses of the legacy name `LearnableParameter()` to `ParameterTensor{}`.

  It also matters for the `RowStack()` function, which in BrainScript takes a single parameter that is an array of inputs. The inputs must be separated by a colon (`:`) instead of a comma, e.g. `RowStack (a:b:c)` instead of `RowStack (a, b, c)`.

* Some defaults have been updated, primarily the optional `imageLayout` parameter of `Convolution()`, the pooling operations, and `ImageInput()`. For NDL, these defaulted to `legacy`, whereas now the default is `cudnn` which is required to be compatible with the cuDNN convolution primitives. (All NDL code samples explicitly specify this parameter as `cudnn` already.)

*   BrainScript's parser is more restrictive:

    - Identifiers are now case-sensitive. Built-in functions use PascalCase (e.g. `RectifiedLinear()`), and built-in variables and parameter names use camelCase (e.g. `modelPath`, `criterionNodes`), as do option strings (`init="fixedValue"`, `tag="criterion"`).
      Note that for names of optional parameters, incorrect spellings are not always caught as an error. Instead, some incorrectly spelled optional parameters are just ignored. An example are the 'special nodes' definitions. Their correct spelling for those is now:

            featureNodes    = ...
            labelNodes      = ...
            criterionNodes  = ...
            evaluationNodes = ...
            outputNodes     = ...

    - Abbreviated alternative names are no longer allowed, such as `Const()` should be `Constant()`, `tag="eval"` should be `tag="evaluation"`, and `evalNodes` is now `evaluationNodes`.
    - Some mis-spelled names were corrected: `criteria` is now `criterion` (likewise `criterionNodes`), `defaultHiddenActivity` is now `defaultHiddenActivation`.
    - The `=` sign is no longer optional for function definitions.
    - While it is allowed to use brackets for blocks (`[ ... ]`), it is deprecated. Use curly braces (`{ ... }`).
    - Option labels must be quoted as strings, e.g. `init="uniform"` rather than `init=uniform`. Without the quotes, BrainScript will fail with an error message saying that the symbol `uniform` is unknown.
    - The BrainScript primitives that create parameters (`Input{}` and `ParameterTensor{}`) should use curly braces for their arguments (e.g. `f = Input{42}`). This is a convention that is not enforced but recommended going forward.

    This more restricted syntax is still accepted by `NDLNetworkBuilder`, so we recommend to first make these syntactical changes and test them with *NDL*, before actually changing to BrainScript.

**Step 4. Remove `NDLNetworkBuilder` from "write" and "test" sections**. Please review your "write" and "test" sections for `NDLNetworkBuilder` sections, and remove them. Some of our stock NDL examples have extraneous `NDLNetworkBuilder` sections. They are not used and should not be there. If your configuration is based on one of these examples, you may have such sections as well. They used to be ignored, but with the BrainScript update, defining a new network in these sections now has a meaning (model editing), so they are no longer ignored and therefore should be removed.

### `NDLNetworkBuilder` reference (deprecated)

The syntax of the deprecated `NDLNetworkBuilder` is:

    NDLNetworkBuilder = [
        networkDescription = "yourNetwork.ndl"
    ]

The `NDLNetworkBuilder` block has the following parameters:

* `networkDescription`: the file path of the network description file. With the deprecated `NDLNetworkBuilder`, it was customary to use the file extension `.ndl`. If there is no `networkDescription` parameter specified then the network description is assumed to be inlined in the same `NDLNetworkBuilder` subblock, specified with the `run` parameter below. Note that only one file path may be specified via the `networkDescription` parameter. To load multiple files of macros, use the `ndlMacros` parameter.

* `run`: the block of the NDL that will be executed. If an external NDL file is specified via the `networkDescription` parameter, the `run` parameter identifies a block in that file. This parameter overrides any `run` parameters that may
already exist in the file. If no `networkDescription` file is specified, the `run` parameter identifies a block in the current configuration file.

* `load`: the blocks of NDL scripts to load. Multiple blocks can be specified via a ":" separated list. The blocks specified by the `load` parameter typically contain macros for use by the `run` block. Similar to the `run` parameter, the `load` parameter identifies blocks in an external NDL file and overrides any `load` parameters that may already exist in the file, if a file is specified by the `networkDescription` parameter. If no `networkDescription` file is specified, `load` identifies a block in the current configuration file.

* `ndlMacros`: the file path where NDL macros may be loaded. This parameter is usually used to load a default set of NDL macros that can be used by all NDL scripts. Multiple NDL files, each specifying different sets of macros, can be loaded by specifying a "+" separated list of file paths for this `ndlMacros` parameter. In order to share macros with other command blocks such as NDL's model-editing language (MEL) blocks, you should define it at the root level of the configuration file.

* `randomSeedOffset`: a non-negative random seed offset value in initializing the learnable parameters. The default value is `0`. This allows users to run experiments with different random initialization.
