---
title:   CNTK_2_0_Beta_8_Release_Notes
author:    chrisbasoglu
date:    04/02/2017
ms.author:   cbasoglu
ms.date:   04/02/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK_2_0_Beta_8_Release_Notes

This is a summary of new features delivered with the Beta 8 release of CNTK V.2.0.

## Breaking changes

The are **two breaking changes** in this release. Please, read this section carefully:

* The naming of output variables of Function in CNTK Library API has been changed: if the function has single output, the name of output variable is the same as the function name. In case of multiple outputs, they are named as function name plus the output order as suffix, e.g.  `Foo.1`, `Foo.2`, etc. 

* Batch normalization.
  * If cuDNN batch normalization engine is not available, the CNTK batch normalization engine is used as a fall back.
  * A model trained with cuDNN batch normalization can now be inferred on CPU.

## Python API

The following updates are introduced to Python API:

* CNTK now supports Python versions 2.7, 3.4, and 3.5, for both Windows and Linux. Please see [binary and source setup](../Setup-CNTK-on-your-machine.md) instructions to find out about how to select Python version.
* Max unpooling is now accessible from Python
* New feature: ```as_block``` function enables hierarchical function graph. See detailed description in [CNTK Python API Documentation](https://cntk.ai/pythondocs/cntk.ops.html?highlight=as_block#cntk.ops.as_block).
* New Example: [Feature extraction using a trained model in Python API](https://github.com/Microsoft/CNTK/tree/v2.0.beta8.0/Examples/Image/FeatureExtraction).

## Windows Version. Visual Studio 2015 support

Starting from this release Windows version of CNTK is being built with **Visual Studio 2015**. Make sure to install the Visual C++ Redistributable Package for Visual Studio 2015, if you're installing the binary drop manually (included in the package).

If you maintain a developer environment, please read the article [Migrate VS 2013 to VS 2015](../Setup-Migrate-VS13-to-VS15.md). You may also consider [Script driven migration](../Setup-CNTK-with-script-on-Windows.md).

## CNTK Evaluation library. NuGet package

The first version of [CNTK Library Managed Eval API](../CNTK-Library-Evaluation-on-Windows.md) is available. It provides a new C# API supporting evaluation of CNTK models using .NET. The new API also supports evaluation on GPU devices, as well as running multiple evaluation requests in parallel. Please see [here](../CNTK-Library-Evaluation-on-Windows.md) for details. 

Examples on usage of the new CNTK C# API are available in [`CNTKLibraryEvalExamples.sln`](https://github.com/Microsoft/CNTK/blob/v2.0.beta8.0/Examples/Evaluation/CNTKLibraryEvalExamples.sln) located in [Examples/Evaluation](https://github.com/Microsoft/CNTK/tree/v2.0.beta8.0/Examples/Evaluation) (available also in CNTK Binary Packages). 

The structure of [CNTK NuGet packages](../NuGet-Package.md) has changed with two additional packages introduced: 
* (*New*) `CNTK.CPU`. Contains [CNTK Library C# Eval API](../CNTK-Library-Evaluation-on-Windows.md), and used for CPU only machines.
* (*New*) `CNTK.GPU`. Contains [CNTK Library C# Eval API](../CNTK-Library-Evaluation-on-Windows.md), and used for GPU enabled machines.
* `Microsoft.Research.CNTK.CpuEval-mkl`. Contains [CNTK EvalDll API](../EvalDll-Evaluation-Overview.md). It supports CPU only.  

**IMPORTANT!** In Visual Studio *Manage Nuget Packages* Window change the default option *Stable Only* to *Include Prerelease*. Otherwise the packages will not be visible. The Package version should be ```2.0-beta8```.

## Custom MKL Library v.3

If you are working with CNTK codebase, please be aware that [**Version 3 of CNTK Custom MKL Library**](https://www.microsoft.com/en-us/cognitive-toolkit/download-math-kernel-library/) is now available. You should use this version in your environment from now on.

## CNTK Runtime Images at Docker Hub

CNTK Runtime packages are now available as [Public Images at Docker Hub](https://hub.docker.com/r/microsoft/cntk/).

**IMPORTANT!** There will be a small time delay (expected 3-5 working days) between a new CNTK Release and its availability on Docker Hub. E.g. on the day of v.2.0 Beta 8 Release (this release) only v.2.0 Beta 7 Images are available at Docker Hub. We will announce the availability of Docker Hub images for each version both at CNTK Release Page and in the News.

See more on CNTK as Docker Images in this [article](../CNTK-Docker-Containers.md).
