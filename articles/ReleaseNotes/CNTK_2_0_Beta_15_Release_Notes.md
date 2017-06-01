---
title:   CNTK_2_0_Beta_15_Release_Notes
author:    chrisbasoglu
date:    03/15/2017
ms.author:   cbasoglu
ms.date:   03/15/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK_2_0_Beta_15_Release_Notes

This is a summary of new features delivered with the Beta 15 release of CNTK V.2.0.


## Breaking changes

This release contains the following **breaking changes**:

* `NDArrayView`'s `data()` method is now a property 
* Module restructuring: we refactored functionalities into more logical namespaces, although for the time being we keep the old one for backward compatibility. It is recommended to use the new layout, because the old one will be removed in the next version. Here is the breaking change:
  * Readers are exposed through `cntk.io` namespace, they are not imported to the root `cntk` namespace anymore.
  * Print progress has been moved to `cntk.logging`, and will be removed from `cntk.util` soon.
  * Losses and metrics has been moved to `cntk.losses` and `cntk.metrics` respectively.
  * All learners are now under `cntk.learners` namespace; `cntk.learner` is deprecated.
  * Train, training, and distributed training have been moved to `cntk.train` namespace.
  * `one_hot` function is now a static method in `Value` class.
* Evaluating a node that has only batch axis and no sequence axis, previously always returned the result containing a sequence dimension. This was fixed, i.e. it will now only output the batch axis.
* `adam_sgd` is renamed to `adam`; `fsadagrad` is introduced; `adam_sgd` is deprecated.

## New and updated features

* See sections below on new features in Python and C# API
*  In addition to pre-existing python support, added support for TensorBoard output in BrainScript. [Read more here](../Using-TensorBoard-for-Visualization.md).
* Learners can now be implemented in pure Python by means of `UserLearners`. [Read more here](https://docs.microsoft.com/en-us/python/cognitive-toolkit/extend#user-defined-learners).
* New debugging helpers: `dump_function()`, `dump_signature()`.
* Tensors can be indexed using advanced indexing. E.g. `x[[0,2,3]]` would return a tensor that contains the first, third and fourth element of the first axis.

## Python API

We have made the following updates to the [Layers Library](https://docs.microsoft.com/en-us/python/cognitive-toolkit/layerref). **IMPORTANT!** The changes in this section are NOT yet reflected in the main Python API documentation. We will update it soon; for the time being please use the following information.

* The Layers library is CNTK’s functional-style API:
  * Neural networks are viewed as function objects (which are composable and can share parameters).
  * Very expressive and easy to use (e.g. sequence-to-sequence with greedy decoder in 2 pages without custom operations).
  * Tight integration with native Python syntax: Python call syntax, parameters as members (`my_convo_layer.W`), types for input shapes (`Sequence[SparseTensor[30000]]`), slices.
  * See an overview at [this slide](https://github.com/Microsoft/CNTK/wiki/ppt/Layers-vs-API.pdf).
* New recurrent higher-order layers:
  * `RecurrenceFrom()` - recurrence with initial state from data, e.g. sequence-to-sequence
  * `Fold()` - recurrence to get final state.
  * `UnfoldFrom()` - sequence decoding.
  * Step functions can be any function or layer with the right signature, including compositions and/or user-defined, e.g. `Fold(element_max)` for max-pooling over a sequence; `Recurrence(GRU(500) >> Dense(200))` for recurrence with projection.
* Miscellaneous new layers: `GRU()`, `Activation()`, `Label()`, `LayerNormalization()`, `SequentialClique()`, `ResNetBlock()`, `PastValueWindow()`, `Attention()`.
* `Convolution()` now supports convolving along sequence axis, e.g. for NLP (m-grams).
* Default options can now be layer-specific `(with default_options_for (MaxPooling, pad=False)):...`).

## CNTK C# Evaluation Library V2:

* Added `LoadModel()` from memory buffer, available for C++ and C#. See [Example](https://github.com/Microsoft/CNTK/tree/v2.0.beta15.0/Examples/Evaluation/CNTKLibraryCSEvalCPUOnlyExamples).
* New [C# examples for evaluating multiple requests in parallel](../CNTK-Eval-Examples.md#examples-for-evaluating-multiple-requests-in-parallel).

## CNTK NuGet package

A new set of NuGet Packages is provided with this Release. 

**IMPORTANT!** In Visual Studio *Manage Nuget Packages* Window change the default option *Stable Only* to *Include Prerelease*. Otherwise the packages will not be visible. The Package version should be ```2.0-beta15```.

## Bug fixes

* `ProgressPrinter`: Fixed bug that incorrectly showed slowdowns in samples per second.  Added back the `log()` API call.
* Various bug fixes in C# API.
