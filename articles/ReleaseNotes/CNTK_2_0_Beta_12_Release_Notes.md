---
title:   CNTK_2_0_Beta_12_Release_Notes
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   02/23/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK_2_0_Beta_12_Release_Notes

This is a summary of new features delivered with the Beta 12 release of CNTK V.2.0.

## Breaking changes

This release contains the following **breaking changes**:

*  `crop`, `scale`, `mean`, and `color` transforms are no longer static methods of `ImageDeserializer` and have been moved to `cntk.io.transforms`.
* `ReaderConfig` is renamed to `_ReaderConfig` (this helper class is intended for internal use only)
*  Starting from this Release CNTK libraries from CNTK NuGet Packages will be signed using `sha256` Digest algorithm only. `sha1`-based signatures will no longer be used.

## New and updated features

* More built-in activation functions: Leaky ReLU, ELU and Parametric ReLU.
* `Argmax` and `Argmin` are now supported.
* It is now possible to write your own optimizer in Python by inheriting from `UserLearner` and overriding the update method.
* HTK deserializers are now exposed in Python. All deserializers are exposed in C++.
* The memory pool implementation of CNTK has been updated with a new global optimization algorithm. Hyper memory compression has been removed.
* Significantly improved performance of `numpy` interop
* Improved progress logging API and performance. Progress tracking code moved from Python to a new C++ `ProgressWriter` class. Users can now pass progress writers upon instantiating a Trainer, and do not have to use them explicitly in their training loop.
* New functionality of existing operators
  * `PastValue()` and `FutureValue()` now accept a data-dependent initial state, for sequence-to-sequence modeling.
  * `Where()` and `gather()` now interprets the flags as a repeat factor, which allows to duplicate steps. Factors can be fractional.
  * `ReconcileDynamicAxis()` now accepts a data input without dynamic axis, to easily broadcast a constant.
* Improved `graph.plot()`:
  * Improved visual quality and compactness.
  * New output formats: PDF and SVG.
* Introducing `stop_gradient` operator that acts as an identity in the forward pass but stops gradients from flowing back to its inputs in the backward pass.
* New features in CNTK NuGet Package. See section on NuGet Package below.

## CNTK Python API

We improved the installation of CNTK on Python. As a first step you will find some simple instructions how to `pip install` the CPU version of CNTK on Anaconda 3 on [this page](../Setup-Windows-Python.md). Similar improvements for Linux as well as CNTK GPU versions will follow shortly.

## CNTK C++ API

The following is added to CNTK C++ API:

* `CreateBatch`, `CreateSequence`, `CreateBatchOfSequence` - create *Value* object representing batch, sequence and batch of sequence.
* `CopyVariableValueTo` -  copy data out of *Value* object.
* `FindByName` - find a function with the given name in the Function graph.
* `FindAllWithName` - find a list of functions with the given name in the Function graph
* Progress tracking code moved from Python to a new C++ `ProgressWriter` class. Users can now pass progress writers upon instantiating a Trainer, and do not have to use them explicitly in their training loop.

## New Examples and Tutorials

* [New Eval examples for RNN models](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryCSEvalCPUOnlyExamples/CNTKLibraryCSEvalExamples.cs) are added to C# Evaluation examples. Also examples for using one-hot vector as input are available.  

## CNTK NuGet package

A new set of NuGet Packages is provided with this Release. The packages have the following new features:
* CNTK V2 C++ Library is now published as a part of Nuget packages.
* `CNTK.GPU` and `CNTK.CPUOnly` Nuget packages now contain header files, libraries and all dependent dll libraries required to use the C++ library for training and evaluation. Both debug and release builds are supported. 

**IMPORTANT!** In Visual Studio *Manage Nuget Packages* Window change the default option *Stable Only* to *Include Prerelease*. Otherwise the packages will not be visible. The Package version should be ```2.0-beta12```.

**IMPORTANT!** NuGet package in this Release contains a **breaking change** related to the way CNTK libraries are digitally signed. See *Breaking changes* section in the beginning of these Release Notes. 
