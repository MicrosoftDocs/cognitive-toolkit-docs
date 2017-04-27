---
title:   CNTK_2_0_Beta_11_Release_Notes
author:    chrisbasoglu
date:    02/10/2017
ms.author:   cbasoglu
ms.date:   02/10/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK_2_0_Beta_11_Release_Notes

This is a summary of new features delivered with the Beta 11 release of CNTK V.2.0.

## Breaking changes

This release contains the following **breaking changes**:

* Some changes were made to CNTK APIs. Please update your code as follows:
  * All imports from `cntk.blocks` should be changed to `cntk.layers`
  * Change calls to `Trainer()` from `Trainer(model, loss, metric, learners)` to `Trainer(model, (loss, metric), learners)`
  * Calls to `splice()` should be changed from `splice([a, b, c], 'my_name')` to `splice(a, b, c, name='my_name')`
  * Change `LayerStack(N, ...)` to `For(range(N), …)`
  * Calls to `reduce_log_sum()` need to be renamed to `reduce_log_sum_exp()`
  * Explicit calls to `plus()`, `log_add_exp()` and `element_times()` that pass a name must use the keyword `name=...`
  * Change calls to `save_model()` and `restore_model()` to `save()` and `restore()`, respectively. Further, `load_model()` can now optionally be written as `Function.load()`
* CNTK NuGet package:
  * *CNTKLibraryManaged-2.0.dll* now has a *Strong Name* (both GPU and CPU versions). Implementation details available in the description in the header of  [CNTKLibraryManagedDll-CPUOnly-AssemblyInfo.cs](https://github.com/Microsoft/CNTK/blob/master/bindings/csharp/CNTKLibraryManagedDll/CNTKLibraryManagedDll-CPUOnly-AssemblyInfo.cs) and [CNTKLibraryManagedDll-GPU-AssemblyInfo.cs](https://github.com/Microsoft/CNTK/blob/master/bindings/csharp/CNTKLibraryManagedDll/CNTKLibraryManagedDll-GPU-AssemblyInfo.cs). This is in addition to *EvalWrapper.dll* which is strong name enabled since v.1.7.

## New and updated features

* New `reduce_prod` primitive
* Support for reductions across all axes (including batch and sequence axes)
* Improved denominator sharing in Batch Normalization. `batch_normalization (cntk.ops)` now takes an additional required parameter for the running mean sample count, which can be initialized with `constant(0)`
* Added support for randomization window specified in terms of a number of chunks. Fix the default value for randomization window: unless explicitly overridden, it now defaults to 128 chunks. For more details, please see the section that describes `randomizationWindow` parameter:
  * [CTF reader](../BrainScript-CNTKTextFormat-Reader.md#reader-section)
  * [Composite reader](../BrainScript-and-Python---Understanding-and-Extending-Readers.md#general-reader-configuration)
* New Python and BrainScript for VGG16 and 19
* Optimized memory management for convolution workspace, and now a larger (~30% for VGG) minibatch size can be used for training  
* Profiler support in python. See more [here](./Performance-Profiler#for-python)
* Added support in training session for cross validation and preservation of all checkpoints

## New Examples and Tutorials

* Transfer learning example to train custom image classifiers
  * [Code](https://github.com/Microsoft/CNTK/tree/v2.0.beta11.0/Examples/Image/TransferLearning) 
  * [Wiki](../Build-your-own-image-classifier-using-Transfer-Learning.md)

## CNTK Evaluation library. NuGet package

A new set of NuGet Packages is provided with this Release. 

**IMPORTANT!** In Visual Studio *Manage Nuget Packages* Window change the default option *Stable Only* to *Include Prerelease*. Otherwise the packages will not be visible. The Package version should be ```2.0-beta11```.

**IMPORTANT!** NuGet package in this Release contains a **breaking change** related to Assembly Strong Name enabling. See *Breaking changes* section in the beginning of these Release Notes. 

## Bug fixes

* Re-enable image rendering in python notebooks when used with GitHub web viewer