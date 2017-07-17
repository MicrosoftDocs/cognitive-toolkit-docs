---
title:   CNTK_1_7_1_Release_Notes
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   09/30/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK_1_7_1_Release_Notes

This is a summary on what's new in CNTK 1.7.1 Binary Release. 

## Breaking changes

The are **two breaking changes** in this release. Please, read this section carefully:

* Layers library default initialization was changed from ```heNormal``` to ```glorotNormal```. Pass ```init=”heNormal”``` to get 1.7 behaviour.
* ```fsAdagrad``` had a bug. Learning rates must be retuned. To somewhat approximate the old behaviour, scale by ```sqrt(number of parameter tensors)/400```.

## BrainScript

We have the following improvements in BrainScript.

* BrainScript now allows relational operators inline, and scalar constants get automatically casted to ```Constant()```. Example:
```
HammingLoss (y, p) = ReduceSum (y != (p > 0.5))
```
compare this to the previous syntax
```
HammingLoss (y, p) = ReduceSum (NotEqual (y, (Greater (p, Constant(0.5)))))
```
* ```edit``` action can now use BrainScript.

## CNTK Model Evaluation library

The following changes and improvements are introduced in V.1.7.1:

* Model evaluation support for Azure Applications. The [Evaluate a model in an Azure WebApi page](../Evaluate-a-model-in-an-Azure-WebApi.md) provides detailed steps. 
* The Extended Eval interface adds support for evaluation of RNN model.

## Deterministic Algorithms

* Adding ```forceDeterministicAlgorithms=true``` to the configuration will force use of deterministic algorithms if possible. This flag will force use of only a single thread for MKL and OMP operations.
* **IMPORTANT!** The determinism changes require a new version of the CNTK Custom MKL (v2). For binary downloads this is included in the package. If you build CNTK from sources please follow the installation instructions for [Windows](../Setup-CNTK-on-Windows.md#mkl) or [Linux](../Setup-CNTK-on-Linux.md#mkl).

## Performance optimization

We have made the following performance related improvements:

* GPU prefetch with pinned memory is implemented for the new readers (HTKDeserializers, CNTKTextFormat and Image reader)
* Type optimizations in the image reader that decrease memory pressure

## Other changes

* Optimized logging—most bulk logging output now require ```traceLevel=1```
* ```ParallelTrain.numGradientBits``` can now change over epochs

## Bug Fixes

You will find the following fixes in this release:

* Fix for packed sequences
* Correctness for Sequence2Sequence
* Automated minibatch scaling 
* Optimized ```lstm``` from cudnn5.1
* Automatic minibatch-sizing no longer affects accuracy
* Improved performance for certain kinds of recurrent networks (```PastValue()```)
* ```fanout``` in ```glorot``` initialization is now correct
* Fix for dimension error in cudnn RNN wrapper
* ```fsAdagrad``` denominator is now aggregated correctly
* ```LSTMBlock{}``` and ```StabilizerLayer{}``` no longer create parameters from inside ```apply()```
* Improved default for ```BatchNormalizationLayer{}`` time constant

## Python support

Starting from v.1.5 you can use the preview of Python API. See [CNTK v.1.5 Release Notes](./CNTK_1_5_Release_Notes.md) for further instructions.
