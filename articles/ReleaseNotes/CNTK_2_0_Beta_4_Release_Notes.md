---
title:   CNTK_2_0_Beta_4_Release_Notes
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   11/22/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.prod: cntk
ms.devlang:   NA
---

# CNTK_2_0_Beta_4_Release_Notes

This is a summary of new features delivered with the Beta 4 release of CNTK V.2.0.

## ASGD/Hogwild! training using Microsoft’s Parameter Server (Project Multiverso)

This release introduces Asynchronous Stochastic Gradient Descent (ASGD)/Hogwild! training parallelization support using Microsoft’s Parameter Server ([Project Multiverso](https://github.com/Microsoft/multiverso)).

## CNTK Python API. Distributed Scenarios support

This release add the support of Distributed scenarios. See more in the sections on Distributed scenarios in [ConvNet](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Image/Classification/ConvNet/Python/README.md) and [ResNet](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Image/Classification/ResNet/Python/README.md) examples. (All examples are also available as a part of CNTK Binary Packages)

## Memory compression

This release introduces a *Memory Compression*, an ability to trade off memory usage with compute. See how to enable the feature in the [Top level configurations](../BrainScript-Top-level-configurations.md#forcedeterministicalgorithms) page (```hyperCompressMemory``` variable).

## Reorganizing location of Examples and Tutorials

We continue re-organizing and re-arranging CNTK Examples and Tutorials. Please, be aware that as a part of the process we are gradually removing outdated Python examples.

## CNTK Docker image with 1bit-SGD support

You may now compile and install CNTK with 1bit-SGD support as a Docker container. Thus all three CNTK configurations may be implemented as Docker containers. See more on [CNTK Docker containers here](https://github.com/Microsoft/CNTK/tree/release/latest/Tools/docker/README.md). 

## CNTK Evaluation library

**Please note that in this release there will be NO update of CNTK NuGet package.** The latest package is available in CNTK V.2.0 Beta 3.


## Stability Improvements

We continue fine tuning new features and fixing different bugs - thank you once again for the constant feedback. You are not required to adopt your code or models to take an advantage of these improvements.
