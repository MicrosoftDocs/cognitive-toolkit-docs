---
title:   Model  Editing with BrainScript
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   03/27/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   brainscript
---

# Model  Editing with BrainScript

(Note: Older versions of CNTK used "MEL" (Model Editing Language) for this purposes. We are still in the process of converting the examples. For documentation on MEL, please see the [here](https://github.com/Microsoft/CNTK/tree/release/latest/Documentation/Documents/Model%20Editing%20Language.md).)

CNTK allows models to be edited after the fact. This is done by creating a new model while cloning (parts of) an existing model with modifications applied. For this, CNTK provides three basic functions:

* `BS.Network.Load()` to load an existing model
* [`BS.Network.CloneFunction()`](./CloneFunction.md) to extract a section of an existing model for reuse
* `BS.Network.Edit()` to clone a model with node-by-node modifications applied

The editing operation is not a separate step. Rather, a command that should work off a modified model would not specify a `modelPath` to load the model from, but rather a `BrainScriptNetworkBuilder` section that loads the model inside and constructs a new model off the loaded one on the fly.

## Example: Discriminative pre-training

Discriminative pre-training is a technique where a deep network is created by training a sequence of shallower networks. Start with a 1-hidden layer network, train to partial convergence, then remove the output layer, add a new hidden layer, and add a new output layer. Repeat until the desired number of hidden layers is reached.

Let us assume with a very simple starting model

    BrainScriptNetworkBuilder = [
        N = 40; M = 9000; H = 512
        W1   = Parameter (H, N); b1   = Parameter (H)
        Wout = Parameter (M, H); bout = Parameter (M)
        x = Input (N, tag=‘feature’) ; labels = Input (M, tag=‘labels’)
        h1 = Sigmoid (W1 * x  + b1)
        z  = Wout * h1 + bout
        ce = CrossEntropyWithSoftmax (labels, z, tag=‘criterion’)
    ]

Let us train this model and save under "model.1.dnn". Next we want to train a model with two hidden layers, where the first hidden layer is initialized from the values trained above. To do so, we create a separate training action that creates a new model, but reusing parts of the previous one, as follows:

    BrainScriptNetworkBuilder = {
        # STEP 1: load 1-hidden-layer model
        inModel = BS.Network.Load ("model.1.dnn")
        # get its h1 variable --and also recover its dimension
        h1 = inModel.h1
        H = h1.dim
        # also recover the number of output classes
        M = inModel.z.dim

        # STEP 2: create the rest of the extended network as usual
        W2   = Parameter (H, H); b2   = Parameter (H)
        Wout = Parameter (M, H); bout = Parameter (M)
        h2 = Sigmoid (W2 * h1  + b2)
        z  = Wout * h2 + bout
        ce = CrossEntropyWithSoftmax (labels, z, tag=‘criterion’)
    }

First, STEP 1 uses `Load()` to load the network into a BrainScript variable. The network behaves like a BrainScript record, where all top-level nodes (all nodes that do not contain a `.` or `[` in their node names) are accessible through record syntax. A new network can reference any node in a loaded network. In this example, the loaded network contains a node `h1` which is the output of the first hidden layer, and a node `z` which is the unnormalized log posterior probability of the output classes (input to the Softmax function). Both nodes can be accessed from BrainScript through dot syntax, e.g. `inModel.h1` and `inModel.z`.

Note that constants are not stored in models, so neither `N` nor `M` are available from the model. It is, however, possible to reconstruct them from the loaded model. For that purpose, computation nodes also behave like BrainScript records and expose a `dim` property.

Next, STEP 2 constructs the rest of the new network using regular BrainScript. Note that this new section simply uses the node `h1` from the input model as an input, like it would use any other node. Referencing a node from the input network will automatically make all nodes that this node depends on also part of the newly created network. For example, the input node `x` will automatically become part of the new network.

Also note that the output layer is constructed anew. This way, its model parameters will get freshly created. (To not do that and instead reuse the existing parameters, one could use `inModel.Wout`, but note that does not make sense from the network design point of view in this particular example.)

## Example: Using a pre-trained model

The following is an example of using a pre-trained model (from file `"./featext.dnn"`) as a feature extractor:

    BrainScriptNetworkBuilder = {
        # STEP 1: load existing model
        featExtNetwork = BS.Network.Load ("./featext.dnn")

        # STEP 2: extract a read-only section that is the feature extractor function
        featExt = BS.Network.CloneFunction (
                      featExtNetwork.input,    # input node that AE model read data from
                      featExtNetwork.feat,     # output node in AE model that holds the desired features
                      parameters="constant")   # says to freeze that part of the network

        # STEP 3: define the part of your network that uses the feature extractor
        # from the loaded model, which above we isolated into featExt().
        # featExt() can be used like any old BrainScript function.
        input = Input (...)
        features = featExt (input)  # this will instantiate a clone of the above network

        # STEP 4: and add the remaining bits of the network in BrainScript, e.g.
        h = Sigmoid (W_hid * features + b_hid) # whatever your hidden layer looks like
        z = W_out * h + b_out
        ce = CrossEntropyWithSoftmax (labels, z)
        criterionNodes = (ce)
    }

STEP 1 uses `Load()` to load the network into a BrainScript variable.

STEP 2 uses `CloneFunction()` to clone the feature-extraction related section from the loaded network, which is the sub-graph that connects `featExtNetwork.input` to `featExtNetwork.feat`. Since we specified `parameters="constant"`, all parameters that `featExtNetwork.feat` depends on are also cloned and made read-only.

In STEP 3 and 4, the new network is defined. This is done like any other BrainScript model, only that now we can use the `featExt()` function in doing so.

### Problems with node names with `.` `[` and `]`
To reference a node in a network that contains `.` or `[` or `]`, replace those characters by `_`.
E.g., if `network` contains a node called `result.z`, `network.result.z` will fail;
instead say `network.result_z`.

## Example: Modifying nodes of an existing network

To modify inner parts of an existing network, one would actually clone the network, while modifications are applied as part of the cloning process. This is accomplished by `BS.Network.Edit()`. `Edit()` will iterate over all nodes of a network, and will offer each node, one by one, to lambda functions passed by the caller. Those lambda functions can then inspect the node and either return the node unmodified, or return a new node in its place. `Edit()` will iterate over nodes in unspecified order. If a replacement node references a network node that in turn was replaced, `Edit()` will, as a final step, update all such references to the respective replacements (aka "do the right thing").

TODO: Example.

Next: [Full Function Reference](./BrainScript-Full-Function-Reference.md)
