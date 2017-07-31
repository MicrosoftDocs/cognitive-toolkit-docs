---
title:   BrainScript Full Function Reference
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   03/09/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# BrainScript Full Function Reference

This section provides information on BrainScript built-in functions.

The declarations of all built-in functions can be found in the [`CNTK.core.bs`](https://github.com/Microsoft/CNTK/tree/master/Source/CNTK/BrainScript/CNTKCoreLib) located next to the CNTK binary.

The primitive operations and layers are declared in the global namespace. Additional operations are declared in namespaces, and will be given with the respective prefix (e.g. `BS.RNN.LSTMP`).

## Layers
 - [`DenseLayer`](./BrainScript-Layers-Reference.md#denselayer-linearlayer)` {outDim, bias= true, activation=Identity, init='uniform', initValueScale=1}`  
 - [`ConvolutionalLayer`](./BrainScript-Layers-Reference.md#convolutionallayer)` {numOutputChannels, filterShape, activation = Identity,`  
                      `init = "uniform", initValueScale = 1,`  
                      `stride = 1, pad = false, lowerPad = 0, upperPad = 0,` 
                      `bias=true}`  
 - [`MaxPoolingLayer`](./BrainScript-Layers-Reference.md#maxpoolinglayer-averagepoolinglayer)` {filterShape, stride = 1, pad = false, lowerPad = 0, upperPad = 0}`  
 - [`AveragePoolingLayer`](./BrainScript-Layers-Reference.md#maxpoolinglayer-averagepoolinglayer)` {filterShape, stride = 1, pad = false, lowerPad = 0, upperPad = 0}`  
 - [`EmbeddingLayer`](./BrainScript-Layers-Reference.md#embeddinglayer)` {outDim, embeddingPath = '', transpose = false}`  
 - [`RecurrentLSTMLayer`](./BrainScript-Layers-Reference.md)` {outputDim, cellShape = None, goBackwards = false, enableSelfStabilization = false}`  
 - [`DelayLayer`](./BrainScript-Layers-Reference.md#delaylayer)` {T=1, defaultHiddenActivation=0}`  
 - [`Dropout`](./Dropout.md)  
 - [`BatchNormalizationLayer`](./BrainScript-Layers-Reference.md#batchnormalizationlayer-layernormalizationlayer-stabilizerlayer)` {spatialRank = 0, initialScale = 1,  
                         normalizationTimeConstant = 0, blendTimeConstant = 0,  
                         epsilon = 0.00001, useCntkEngine = true}`  
 - [`LayerNormalizationLayer`](./BrainScript-Layers-Reference.md#batchnormalizationlayer-layernormalizationlayer-stabilizerlayer)` {initialScale = 1, initialBias = 0}`  
 - [`StabilizerLayer{}`](./BrainScript-Layers-Reference.md#batchnormalizationlayer-layernormalizationlayer-stabilizerlayer)  
 - [`FeatureMVNLayer{}`](./BrainScript-Layers-Reference.md#featuremvnlayer)  

## Layer building
 - [`Sequential()`](./Sequential.md)  

## Activation functions
 - [`Sigmoid`](./BrainScript-Activation-Functions.md)`(x)`  
 - [`Tanh`](./BrainScript-Activation-Functions.md)`(x)`  
 - [`ReLU`](./BrainScript-Activation-Functions.md)`(x)`  
 - [`Softmax`](./BrainScript-Activation-Functions.md)`(x, axis=0)`  
 - [`LogSoftmax`](./BrainScript-Activation-Functions.md)`(x)`  
 - [`Hardmax`](./BrainScript-Activation-Functions.md)`(x)`  

## Elementwise operations, unary
 - [`Abs`](./Unary-Operations.md)` (x)`  
 - [`Ceil`](./Unary-Operations.md)` (x)`  
 - [`Cosine`](./Unary-Operations.md)` (x)`  
 - [`Clip`](./Unary-Operations.md)` (x, minValue, maxValue)`  
 - [`Exp`](./Unary-Operations.md)` (x)`  
 - [`Floor`](./Unary-Operations.md)` (x)`  
 - [`Log`](./Unary-Operations.md)` (x)`  
 - [`Negate`](./Unary-Operations.md)` (x)`  
   [`-x`](./Unary-Operations.md)  
 - [`BS.Boolean.Not`](./Unary-Operations.md)` (b)`  
   [`!x`](./Unary-Operations.md)  
 - [`Reciprocal`](./Unary-Operations.md)` (x)`  
 - [`Round`](./Unary-Operations.md)` (x)`  
 - [`Sin`](./Unary-Operations.md)` (x)`  
 - [`Sqrt`](./Unary-Operations.md)` (x)`  

## Elementwise operations, binary
 - [`ElementTimes`](./Binary-Operations.md)` (x, y)`  
   [`x .* y`](./Binary-Operations.md)  
 - [`Minus`](./Binary-Operations.md)` (x, y)`  
   [`x - y`](./Binary-Operations.md)  
 - [`Plus`](./Binary-Operations.md)` (x, y)`  
   [`x + y`](./Binary-Operations.md)`  
 - [`LogPlus`](./Binary-Operations.md)` (x, y)`  
 - [`Less`](./Binary-Operations.md)` (x, y)`  
 - [`Equal`](./Binary-Operations.md)` (x, y)`  
 - [`Greater`](./Binary-Operations.md)` (x, y)`  
 - [`GreaterEqual`](./Binary-Operations.md)` (x, y)`  
 - [`NotEqual`](./Binary-Operations.md)` (x, y)`  
 - [`LessEqual`](./Binary-Operations.md)` (x, y)`  
 - [`BS.Boolean.And`](./Binary-Operations.md)` (a, b)`  
   [`BS.Boolean.Or`](./Binary-Operations.md)` (a, b)`  
   [`BS.Boolean.Xor`](./Binary-Operations.md)` (a, b)`  

## Elementwise operations, ternary
 - [`BS.Boolean.If`](./If-Operation.md)` (condition, thenVal, elseVal)`  

## Matrix product and convolution operations
 - [`Times`](./Times-and-TransposeTimes.md)`(A, B, outputRank=1)`  
   [`A * B`](./Times-and-TransposeTimes.md)  
 - [`TransposeTimes`](./Times-and-TransposeTimes.md)`(A, B, outputRank=1)`  
 - [`Convolution`](./Convolution.md)`(weights, x, kernelShape, mapDims=(0), stride=(1), sharing=(true), autoPadding=(true), lowerPadding=(0), upperPadding=(0), imageLayout='CHW', maxTempMemSizeInSamples=0)`  
 - [`Pooling`](./Pooling.md)`(x, poolKind/*'max'|'average'*/, kernelShape, stride=(1), autoPadding=(true), lowerPadding=(0), upperPadding=(0), imageLayout='CHW')`  
 - [`ROIPooling`](./ROIPooling.md)`(x, rois, roiOutputShape, spatialScale=1.0/16.0)`

## Learnable parameters and constants
 - [`ParameterTensor`](./Parameters-And-Constants.md)` {shape, learningRateMultiplier=1.0, init='uniform'/*|gaussian*/, initValueScale=1.0, initValue=0.0, randomSeed=-1, initFromFilePath=''}`  
 - [`Constant`](./Parameters-And-Constants.md#constant)` {scalarValue, rows = 1, cols = 1}`  
 - `BS.Constants.Zero`, `BS.Constants.One`  
   `BS.Constants.True`, `BS.Constants.False`, `BS.Constants.None`  
 - `BS.Constants.OnesTensor (shape)`  
 - `BS.Constants.ZeroSequenceLike (x)`

## Inputs
 - [`Input`](./Inputs.md)` (shape, dynamicAxis='', sparse=false, tag='feature')`  
 - `DynamicAxis{}`
 - `EnvironmentInput (propertyName)`  
   `Mean (x)`, `InvStdDev (x)`  

## Loss functions and metrics
 - [`CrossEntropyWithSoftmax`](./Loss-Functions-and-Metrics.md#crossentropy-crossentropywithsoftmax)` (targetDistribution, nonNormalizedLogClassPosteriors)`  
   [`CrossEntropy`](./Loss-Functions-and-Metrics.md#crossentropy-crossentropywithsoftmax)` (targetDistribution, classPosteriors)`  
 - [`Logistic`](./Loss-Functions-and-Metrics.md#logistic-weightedlogistic)` (label, probability)`  
   [`WeightedLogistic`](./Loss-Functions-and-Metrics.md#logistic-weightedlogistic)`(label, probability, instanceWeight)`  
 - [`ClassificationError`](./Loss-Functions-and-Metrics.md#classificationerror)` (labels, nonNormalizedLogClassPosteriors)`  
 - `MatrixL1Reg(matrix)`  
 - `MatrixL2Reg(matrix)`  
 - `SquareError (x, y)`  

## Reductions
 - [`ReduceSum`](./Reduction-Operations.md)` (z, axis=None)`  
   [`ReduceLogSum`](./Reduction-Operations.md)` (z, axis=None)`  
   [`ReduceMean`](./Reduction-Operations.md)` (z, axis=None)`  
   [`ReduceMin`](./Reduction-Operations.md)` (z, axis=None)`  
   [`ReduceMax`](./Reduction-Operations.md)` (z, axis=None)`  
 - `CosDistance (x, y)`  
 - `SumElements (z)`  

## Training operations
 - [`BatchNormalization`](./BatchNormalization.md)` (input, scale, bias, runMean, runInvStdDev, spatial, normalizationTimeConstant = 0, blendTimeConstant = 0, epsilon = 0.00001, useCntkEngine = true, imageLayout='CHW')`  
 - [`Dropout`](./Dropout.md) `(x)`  
 - `Stabilize (x, enabled=true)`  
   `StabilizeElements (x, inputDim=x.dim, enabled=true)`  
 - `CosDistanceWithNegativeSamples (x, y, numShifts, numNegSamples)`  

## Reshaping operations
 - `CNTK2.Reshape (x, shape, beginAxis=0, endAxis=0)`  
   `ReshapeDimension (x, axis, shape) = CNTK2.Reshape (x, shape, beginAxis=axis, endAxis=axis + 1) `  
   `FlattenDimensions (x, axis, num) = CNTK2.Reshape (x, 0, beginAxis=axis, endAxis=axis + num)`  
   `SplitDimension (x, axis, N) = ReshapeDimension (x, axis, 0:N)`  
 - `Slice (beginIndex, endIndex, input, axis=1)`  
   `BS.Sequences.First (x) = Slice (0,  1, x,  axis=-1)`  
   `BS.Sequences.Last (x)  = Slice (-1, 0, x,  axis=-1)`  
 - `Splice (inputs, axis=1)`  
 - `TransposeDimensions (x, axis1, axis2)`  
   `Transpose (x) = TransposeDimensions (x, 1, 2)`  
 - `BS.Sequences.BroadcastSequenceAs (type, data1)`  
 - [`BS.Sequences.Gather (where, x)`](Gather-and-Scatter.md)  
   [`BS.Sequences.Scatter (where, y)`](Gather-and-Scatter.md)  
   [`BS.Sequences.IsFirst (x)`](Gather-and-Scatter.md#isfirst-islast)  
   [`BS.Sequences.IsLast (x)`](Gather-and-Scatter.md#isfirst-islast)  

## Recurrence
 - [`OptimizedRNNStack`](./OptimizedRNNStack.md)`(weights, input, hiddenDims,  
              numLayers=1, bidirectional=false, recurrentOp='lstm')`
 - `BS.Loop.Previous (x, timeStep=1, defaultHiddenActivation=0)`  
   `PastValue (shape, x, defaultHiddenActivation=0.1, ...) = BS.Loop.Previous (0, shape, ...)`  
 - `BS.Loop.Next (x, timeStep=1, defaultHiddenActivation=0)`  
   `FutureValue (shape, x, defaultHiddenActivation=0.1, ...) = BS.Loop.Next (0, shape, ...)`  
 - `LSTMP (outputDim, cellDim=outputDim, x, inputDim=x.shape, aux=BS.Constants.None, auxDim=aux.shape, prevState, enableSelfStabilization=false)`  
 - `BS.Boolean.Toggle (clk, initialValue=BS.Constants.False)`
 - `BS.RNNs.RecurrentLSTMP (outputDim, cellDim=outputDim, x, inputDim=x.shape, previousHook=BS.RNNs.PreviousHC, augmentInputHook=NoAuxInputHook, augmentInputDim=0, layerIndex=0, enableSelfStabilization=false)`  
 - `BS.RNNs.RecurrentLSTMPStack (layerShapes, cellDims=layerShapes, input, inputShape=input.shape, previousHook=PreviousHC, augmentInputHook=NoAuxInputHook, augmentInputShape=0, enableSelfStabilization=false)`  
 - `BS.RNNs.RecurrentBirectionalLSTMPStack (layerShapes, cellDims=layerShapes, input, inputShape=input.dim, previousHook=PreviousHC, nextHook=NextHC, enableSelfStabilization=false)`  

## Sequence-to-sequence support
 - `BS.Seq2Seq.CreateAugmentWithFixedWindowAttentionHook (attentionDim, attentionSpan, decoderDynamicAxis, encoderOutput, enableSelfStabilization=false)`  
 - `BS.Seq2Seq.GreedySequenceDecoderFrom (modelAsTrained)`  
 - `BS.Seq2Seq.BeamSearchSequenceDecoderFrom (modelAsTrained, beamDepth)`  

## Special-purpose operations
 - `ClassBasedCrossEntropyWithSoftmax (labelClassDescriptorVectorSequence, mainInputInfo, mainWeight, classLogProbsBeforeSoftmax)`  

## Model editing
 - `BS.Network.Load (pathName)`  
 - `BS.Network.Edit (inputModel, editFunctions, additionalRoots)`  
 - [`BS.Network.CloneFunction (inputNodes, outputNodes, parameters="learnable" /*|"constant"|"shared"*/)`](./CloneFunction.md)

## Other
 - `Fail (what)`  
 - `IsSameObject (a, b)`  
 - `Trace (node, say='', logFrequency=traceFrequency, logFirst=10, logGradientToo=false, onlyUpToRow=100000000, onlyUpToT=100000000, format=[])`

## Deprecated
 - [`ErrorPrediction`](./Loss-Functions-and-Metrics.md#classificationerror)` (labels, nonNormalizedLogClassPosteriors)`  
 - `ColumnElementTimes (...) = ElementTimes (...)`  
 - `DiagTimes (...) = ElementTimes (...)`  
 - `LearnableParameter(...) = Parameter(...)`  
 - `LookupTable (embeddingMatrix, inputTensor)`  
 - `RowRepeat (input, numRepeats)`  
 - `RowSlice (beginIndex, numRows, input) = Slice(beginIndex, beginIndex + numRows, input, axis = 1)`  
 - `RowStack (inputs)`  
 - `RowElementTimes (...) = ElementTimes (...)`  
 - `Scale (...) = ElementTimes (...)`  
 - `ConstantTensor (scalarVal, shape)`  
   `Parameter (outputDim, inputDim, ...) = ParameterTensor ((outputDim:input), ...)`  
   `WeightParam (outputDim, inputDim) = Parameter (outputDim, inputDim, init='uniform', initValueScale=1, initOnCPUOnly=true, randomSeed=1)`  
   `DiagWeightParam (outputDim)       = ParameterTensor ((outputDim), init='uniform', initValueScale=1, initOnCPUOnly=true, randomSeed=1)`  
   `BiasParam (dim)                   = ParameterTensor ((dim), init='fixedValue', value=0.0)`  
   `ScalarParam()                     = BiasParam (1)`  
 - `SparseInput (shape, dynamicAxis='', tag='feature')`  
   `ImageInput (imageWidth, imageHeight, imageChannels, imageLayout='CHW', dynamicAxis='', tag='feature')`  
   `SparseImageInput (imageWidth, imageHeight, imageChannels, imageLayout='CHW', dynamicAxis='', tag='feature')`  
 - `MeanVarNorm(feat) = PerDimMeanVarNormalization(feat, Mean (feat), InvStdDev (feat))`  
   `PerDimMeanVarNormalization (x, mean, invStdDev)`,  
   `PerDimMeanVarDeNormalization (x, mean, invStdDev)`    
 - `ReconcileDynamicAxis (dataInput, layoutInput)`  
