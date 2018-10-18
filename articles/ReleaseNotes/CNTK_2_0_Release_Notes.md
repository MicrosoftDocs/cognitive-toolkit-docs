---
title:   CNTK_2_0_Release_Notes
author:    alexeyo
ms.author:   alexeyo
ms.date:   05/26/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.prod: cntk
ms.devlang:   NA
---

## CNTK v.2.0 Release Notes

This is the first production release of the Microsoft Cognitive Toolkit V2. We want to thank everybody who provided valuable feedback and contribution. This release would not have happened without them.

### Highlights of this Release

* CNTK backend for Keras
* Extremely fast binary convolution with Halide
* Java API

### CNTK backend for Keras

We are happy to bring CNTK as a backend for Keras as a beta release to our fans asking for this feature. While there is still feature and performance work remaining to be done, we appreciate early feedback that would help us bake Keras support. For installation instructions and additional details see [this page](https://docs.microsoft.com/en-us/cognitive-toolkit/Using-CNTK-with-Keras).

### Binary convolution with Halide

This release includes [an example](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Extensibility/BinaryConvolution) that implements training of a binary convolution network (see [BinaryConnect by Courbariaux et al.](https://arxiv.org/abs/1511.00363)), and a highly optimized native C++ implementation for fast evaluation of binary convolution models. The C++ binary convolution implementation utilizes the [Halide framework](http://halide-lang.org/) for making optimal use of multi-threading and vector instructions available on modern CPUs. It runs up to 10x faster than an equivalent convolution network trained and evaluated at 32-bit floating point precision.

### CNTK Java API

Starting from CNTK V.2.0 RC 3, a Java API is added to support model evaluation in Java on Windows and Linux. Note that the API is still *experimental* and subject to change. Feedback from the community is greatly appreciated. See the details at [this page](https://docs.microsoft.com/en-us/cognitive-toolkit/CNTK-Library-API#java-api-experimental).

### New additional features

* Random variables can now be used as a part of the graph. The following are now under the `cntk.random` module: `uniform`, `normal`, `bernoulli`, and `gumbel` as well as their corresponding ones with `_like` (e.g. `uniform_like`).
* New learner: Universal learner uses a CNTK graph as the update mechanism.
* New high-level `train()` and `test()` functions that wrap `Trainer`, `training_session`, and `Evaluator`. For usage examples see [Examples/1stSteps](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/1stSteps) and [Tutorials/CNTK_200_GuidedTour.ipynb](https://github.com/Microsoft/CNTK/tree/release/latest/Tutorials/CNTK_200_GuidedTour.ipynb).
* `training_session`, `train()`, and `test()` now also accept data in the form of numpy/scipy arrays (in addition to data reader). Built on the basis of the new class `MinibatchSourceFromData` that wraps numpy/scipy arrays in a reader.

### Breaking changes

This release contains the following **breaking changes**:

* C++ `ReaderCrop` API:
  * `cropSize`, `sideRatio`, `areaRatio` and `aspectRatio` now take `std::pair` instead of `scalar`.
  * `cropSize`: takes `width` and `height`.
  * `sideRatio`, `areaRatio` and `aspectRatio` take `min` and `max` values.
* Starting from CNTK V.2.0 RC3, `cntk.ops.input` and `cntk.ops.sequence.input` have been deprecated. We have reverted back to using `input_variable` and `sequence.input_variable`. The difference from the old `input_variable` behavior is that `cntk.ops.input_variable` no longer has a dynamic axis by default.
* `source` and `mb_size` parameters of `TestConfig` and `CrossValidationConfig` have been renamed to `minibatch_source` and `minibatch_size`. Deprecation messages are shown if the old parameters are used.

### CNTK NuGet package

A new set of NuGet Packages (version 2.0.0) is provided with this release.
