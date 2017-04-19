(thanks to @zpbappi, [Issue #672](https://github.com/Microsoft/CNTK/issues/672))
<br>(for a Python API example go [here](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/FeatureExtraction))

Consider you have trained, say, a deep auto-encoder, which is symmetric around an encoding layer ("input --> encoding --> input" model). Now you would like to use the first half of the network as a constant feature extractor in another network.

To achieve this, define the new network that should use the feature extraction, and use the `BS.Network.Load()` and [`BS.Network.CloneFunction()`](./BS-CloneFunction) operations to incorporate a read-only clone of the pre-trained network as part of the new network. The following shows an example:

    # extract a clone of the pre-trained model into a BrainScript
    # function that can then be used as a feature extractor.
    # Your pre-trained model is expected to have two nodes named 'input' and 'feat'.
    featExtNetwork = BS.Network.Load ("YOUR_TRAINED_FE_MODEL")
    featExt = BS.Network.CloneFunction (
                  featExtNetwork.input,    # input node that FE model read data from
                  featExtNetwork.feat,     # output node in FE model that holds the desired features
                  parameters="constant")   # says to freeze that part of the network

    # define your actual model that is meant to consume the features
    # This example maps features to logits with one hidden layer.
    model (features) = [
        W_hid = ... ; b_hid = ... ; W_out = ... ; b_out = ...
        h = Sigmoid (W_hid * features + b_hid) # whatever your hidden layer looks like
        z = W_out * h + b_out
    ].z

    # define your new network, using featExt() like any old BrainScript function. E.g.
    input = Input (...)
    z = model (featExt (input))
    ce = CrossEntropyWithSoftmax (labels, z)
    featureNodes = (input)
    labelNodes = (labels)
    criterionNodes = (ce)

A key parameter is `parameters="constant"`, which will lock all learnable parameters (setting `learningRateMultiplier=0`) inside so that they won't get updated during further training. It also locks potentially used `BatchNormalization()` operations by placing them into inference mode.

#### How to know the precise node names

CNTK automatically generates node names, which are attempted to be as close as possible to the original BrainScript expressions that generated them (it is not always possible). If the above fails because it cannot find the node name you used, please consult the log output generated from loading the model. You will see lines like this:

    Validating --> input = InputValue() :  -> [1640 x 1 x *1]
    Validating --> L4.y = RectifiedLinear (L4.o) : [2048 x 1 x *1] -> [2048 x 1 x *1]

The precise node names in these two examples are `features` and `L4.y`. E.g. to refer to the former, say `featExtNetwork.input`.

#### Workaround: Referencing nodes with names containing `.` or `[`

The above approach currently has a known bug if the `input` or `feat` node you try to use contains a `.` or a `[` in the node name. This will lead to an "unknown identifier" error. To work around this problem, please replace all `.`, `[`, and `]` into underscores `_`.

In the above example, the desired feature output is named `L4.y`. Saying `featExtNetwork.L4.y` will fail with a message like `unknown identifier 'L4'`. This is because nodes inside a network loaded from file are no longer true BrainScript records.

The workaround is to use `featExtNetwork.L4_y` instead.