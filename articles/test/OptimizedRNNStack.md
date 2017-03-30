Implements the optimized CuDNN5 RNN stack of one or more recurrent network layers.

    OptimizedRNNStack (weights, input,
                       hiddenDims, numLayers = 1,
                       bidirectional = false,
                       recurrentOp='lstm')

### Parameters

* `weights`: one weight matrix containing all model parameters as a single matrix. Use [dimension inference](./Parameters-And-Constants#automatic-dimension-inference), cf. description below.
* `input`: data to apply the stack of one or more recurrent networks to. Must be a sequence, and must not be sparse.
* `hiddenDims`: dimension of the hidden state in each layer and, if bidirectional, of each of the two directions
* `numLayers` (default: 1): number of layers
* `bidirectional` (default: false): if true, the model is bidirectional
* `recurrentOp` (default: `lstm`): select the RNN type. Allowed values: `lstm`, `gru`, `rnnTanh`, `rnnReLU`

### Description

This function gives access to the CuDNN5 RNN, a highly efficient implementation
of a stack of one or more layers of recurrent networks.
We have observed speed-ups on the order of 5, compared to an explicit
implementation as a computation network in BrainScript.
Although it is not as flexible as a BrainScript implementation,
the speed-up of training time may be worth the compromise
(note, however, that such models can only be deployed on machines with a GPU).

The networks can be uni- or bidirectional, and be of the following kind (`recurrentOp` parameter):
* `lstm`: Long Short Term Memory (Hochreiter and Schmidhuber)
* `gru`: Gated Recurrent Unit
* `rnnTanh`: plain RNN with a `tanh` non-linearity
* `rnnReLU`: plain RNN with a rectified linear non-linearity

All weights are contained in a single matrix
that should have `hiddenDims` rows and as many columns as needed to hold all parameters.
Since this can be cumbersome to determine,
you can have the dimension [inferred](./Parameters-And-Constants#automatic-dimension-inference) automatically.
To make sure that random initialization uses the correct fan-in, specify `initOutputRank=-1`:

    W = ParameterTensor {(Inferred:Inferred), initOutputRank=-1}

If you use the `lstm` operation, we recommend to use this primitive through [`RecurrentLSTMLayerStack{}`](./Layers-Reference#recurrentlstmlayer-recurrentlstmlayerstack), which will take care of creating the weights.

#### Training on GPU, deploy on CPU
Currently, it is not possible to deploy an RNN trained as an `OptimizedRNNStack()`
on systems without GPUs.
We believe it is possible to perform a post-training model-editing action
that replaces the `OptimizedRNNStack()` nodes by CPU-compatible native BrainScript expressions
that precisely emulate the CuDNN5 RNN implementation.

### Example

Speech recognition model that consists of a 3-hidden layer a bidirectional LSTM
with a hidden-state dimension per layer and direction of 512:

    features = Input {40}
    W = ParameterTensor {(Inferred:Inferred), initOutputRank=-1, initValueScale=1/10}
    h = OptimizedRNNStack (W, features, 512, numLayers=3, bidirectional=true)
    p = DenseLayer {9000, activation=Softmax, init='heUniform', initValueScale=1/3}
