---
title:   Clone Function
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   08/27/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   brainscript
---

# Clone Function

Copies a part of a model's network into a BrainScript function.

    BS.Network.CloneFunction (inputNodes, outputNodes,
                              parameters="learnable" /*|"constant"|"shared"*/)

## Parameters

* `inputNodes` is an array of 1 or more inputs. It lists the nodes of the originating network that are the inputs of the function to be extracted. When calling the resulting BrainScript function, the cloned function's parameters get substituted for these nodes.
* `outputNodes` is either a single output node or a record of multiple output nodes. These denote which nodes in the originating network are the output of the cloned function. The resulting BrainScript function will return these.
* `parameters` determines how learnable parameters inside the cloned section should be treated. The following values are understood:
  * `"learnable"`: Every learnable parameter in the cloned function will get its own copy, which is henceforth updated by training like any other parameter. This is the default.
  * `"constant"`: Learnable parameters are copied, but then frozen. The cloned function will not get any updates during subsequent training, e.g. if you want to use a feature extractor trained on a large standard training set in a subsequent training on a smaller custom set.
  * `"shared"`: The original learnable parameters will continue to be used in a shared fashion. They will be updated during subsequent training from both their original use and the cloned use. If the BrainScript function returned by `CloneFunction()` is invoked multiple times, all clones will share parameters.

## Return Value

BrainScript function that takes as many input arguments as `inputNodes`, and returns either a scalar if `outputNodes` is a scalar, or a record if `outputNodes` is a record, with matching names.

## Description

`CloneFunction()` is a function for use in [editing](./BrainScript-Model-Editing.md) and creating models. It copies a part of a model's network into a BrainScript function, so that this part of the network can be reused. The result is a BrainScript function that can be used as if this section of the network had been defined inside a regular BrainScript function.

The originating network can be a separate network. This allows to import (part of) an external network that was trained on different data. `CloneFunction()` allows to freeze the model parameters of the clone. This allows an external network to be used as a fixed feature extractor, or to act as a regularizer in an adaptation setting.

The originating network can also be a section of the one being defined currently, and the clone can share its parameters with the original. This allows multiple identical paths through the network operating on different data, for example for setups that symmetrically compare similarity of two inputs, where the feature-extracting layers are shared (and learned jointly) for both inputs. This currently does not work, however, if the originating network section contains a recurrent loop.

The section to be copied is defined by its input and output nodes. Imagine a network plot in which a line is drawn around the subsection to be cloned. This section denoted by the line would then be specified by passing all connections that cross the line to enter the marked area as the `inputNodes` parameter, and all that go out as `outputNodes`. `CloneFunction()` will extract this section into a BrainScript function with a number of parameters equal to the number of `inputNodes`, and the output being either a single node or a dictionary of nodes.


  It is also possible to denote learnable parameters as `inputNodes`. In this case, you can substitute new parameters as arguments to the BrainScript function that `CloneFunction()` creates. Do this if you want to copy a function but learn the parameters from scratch. In this case, it is also possible to change dimensions.

Example use cases:

 - adaptation (KL): a frozen read-only copy of the starting model is used as a KL-regularizer
 - adaptation (FDLR): an injected input transform is trained while the network is fixed
 - image: lower layers of ImageNet networks serve as immutable feature extractors for another image task
 - DSSM: applying the same network subsection to two inputs

### Problems with node names with `.` `[` and `]`
To reference a node in a network that contains `.` or `[` or `]`, replace those characters by `_`.
E.g., if `network` contains a node called `result.z`, `network.result.z` will fail;
instead say `network.result_z`.

### Implementation note
`CloneFunction()` does not actually create BrainScript code under the hood. Instead, it creates a C++ object that behaves like a BrainScript function. `CloneFunction()` itself also does not clone the originating network. It only holds a reference. The actual cloning happens when the function that `CloneFunction()` returns is called.
## Examples

Basic usage:

    # create a BS function by copying a piece of an existing network loaded from disk
    network = BS.Network.Load ("some.dnn")
    net = BS.Network.CloneFunction (network.features, network.logP)
    # apply the copy to a new input
    out = net (myFeatures)
    # This will create a copy of the subsection from network.features to network.logP
    # where all links to network.features get replaced by links to myFeatures.

Example with multiple input and output nodes:

    # This specific example passes two input nodes --> the resulting BS function will have 2 inputs;
    # and it passes a record of output nodes --> the BS function will return a record with the same member names
    network = BS.Network.Load ("some.dnn")
    net = BS.Network.CloneFunction ((network.features:network.labels), [ ce = network.ce ; errs = network.errs ])
    # 'net' is now a BrainScript function with this signature:
    #   CloneFunction (input1, input2) --> [ ce = ... ; errs = ... ]

    # now create a network from the BS function
    myFeatures = Input (13)
    myLabels = Input (42)
    out = net (myFeatures, myLabels) # e.g. myFeatures substitutes the original 'features' node
    criterionNodes = (out.ce)        # and the return value is a record with members 'ce' and 'errs'
    evaluationNodes = (out.errs)

A specific example: Adapting a network, while using the original network as a regularizer (KLD):

    # load network
    network = BS.Network.Load ("some.dnn")
    # create a trainable clone and a read-only reference clone
    adaptNet = BS.Network.CloneFunction (network.features, [ z = network.z ], parameters="learnable")
    refNet   = BS.Network.CloneFunction (network.features, [ z = network.z ], parameters="constant")

    # create the main network
    features = Input (42)
    labels = Input (9000)
    z    = adaptNet (features).z
    zRef = refNet   (features).z
    # training criterion
    # In KL adaptation, labels are a linear interpolation of the one-hot targets
    # and the posteriors produced by the reference network.
    refWeight = 0.9
    kldLabels = labels * (1-refWeight) + Softmax (zRef) * refWeight  # interpolate with ref output
    ce = CrossEntropyWithSoftmax (kldLabels, z) # the CE criterion is taken against these interpolated soft labels
    errs = ErrorPrediction (labels, z)          # errors are of course still counted against the actual labels 
    criterionNodes = (ce)
    evaluationNodes = (errs)
