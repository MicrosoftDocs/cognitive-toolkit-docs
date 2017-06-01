---
title:   CNTK_2_0_RC_2_Release_Notes
author:    chrisbasoglu
date:    05/24/2017
ms.author:   cbasoglu
ms.date:   05/24/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

## CNTK v.2.0 RC 2 Release Notes

With the Release Candidate 2 we ship one of the last previews before the Microsoft Cognitive Toolkit V2 final release by the end of May. There is still time to incorporate your feedback - so keep it coming!

### CNTK Core: new and improved features

* New operators added:
  [pow](https://cntk.ai/pythondocs/cntk.ops.html#cntk.ops.pow),
  [sequence.reduce_max](https://cntk.ai/pythondocs/cntk.ops.sequence.html#cntk.ops.sequence.reduce_max), and
  [sequence.softmax](https://cntk.ai/pythondocs/cntk.ops.sequence.html#cntk.ops.sequence.softmax).
* New feature for Linux source builds:
  * GPU Direct RDMA support in distributed gradients aggregation (enabled through `./configure --gdr=yes`).
  * NCCL support for Python in V2 gradients aggregation
* Additional bug fixes and improvements, including:
  * Added functionality to set and get global tracing verbosity level.
  * Added a mechanism to preserve the local state of a distributed worker in the checkpoint.
  * The initial random seed can now be specified for dropout and random sample nodes, the auto-generated seed values for these nodes are now worker-specific.
  * Progress writers will log changes in learning rates and momentum values.

### CNTK Python API

#### New and improved features

* Support for Python 3.6 for source and binary installation; see
  [setup instructions](https://github.com/Microsoft/CNTK/wiki/Setup-CNTK-on-your-machine).
* `UserMinibatchSource` now allows to write custom minibatch sources and pass them to the CNTK core.
  See [here](https://docs.microsoft.com/en-us/python/cognitive-toolkit/extend#user-defined-minibatch-sources) for documentation.
* CNTK deterministic mode (`cntk.cntk_py.force_deterministic_algorithms()`) can be enabled in all configurations, except for overlapped max pooling.
* Training session switched to distributed test evaluation and cross validation.

### CNTK C#/.NET Managed API

#### New and improved features

* New APIs: `class NDArrayView` and methods, `SetMaxNumCPUThreads()`, `GetMaxNumCPUThreads()`, `SetTraceLevel()`, `GetTraceLevel()`
* Memory and performance optimization.

The updated APIs are described in [this Wiki page](https://github.com/Microsoft/CNTK/wiki/CNTK-Library-Managed-API).

### CNTK NuGet package

A new set of NuGet Packages is provided with this Release. 

**IMPORTANT!** For Visual Studio: In the *Manage Nuget Packages* window change the default option *Stable Only* to *Include Prerelease*. Otherwise the release candidate package of CNTK will not be visible. This Package version is ```2.0.0-rc2```.

