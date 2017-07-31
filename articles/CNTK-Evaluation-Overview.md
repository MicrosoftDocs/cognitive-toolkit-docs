---
title:   CNTK Evaluation Overview
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:  07/31/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK Evaluation Overview

Once you have trained a model, you can use CNTK Eval library to evaluate the model in your own application. CNTK supports model evaluation from C++, Python, C#/.NET, and Java. Starting from v2.1, CNTK also supports Universal Windows Platform (UWP).

Features of the CNTK Evaluation include
* Support both CPU and GPU device.
* [Support multiple evaluation requests in parallel](./CNTK-Eval-Examples.md#examples-for-evaluating-multiple-requests-in-parallel).
* Optimize memory usage by parameter sharing of the same model between multiple threads. This will significantly reduce memory usage when running evaluation in a service environment.

The following pages provide detailed information about model evaluation using CNTK Library.
* [CNTK-library evaluation on Windows](./CNTK-Library-Evaluation-on-Windows.md)
* [CNTK-library evaluation on Linux](./CNTK-Library-Evaluation-on-Linux.md)
* [CNTK-library evaluation with Python](./How-do-I-Evaluate-models-in-Python.md)
* [NuGet-Packages](./NuGet-Package.md)
* [Evaluation in Azure](./Evaluate-a-model-in-an-Azure-WebApi.md)
* [Evaluation on Universal Windows Platform (UWP)](./CNTK-Library-Evaluation-on-UWP.md)

### Legacy Applications using CNTK 1.0

Prior to the CNTK 2.0 version, the CNTK EvalDLL was used to evaluate model trained by using cntk.exe with BrainScript. The EvalDLL 
is still supported, but works only for the model created by cntk.exe with BrainScript. It can not be used to evaluate models created by CNTK 2.0 or later using Python. We strongly recommend to use the latest CNTK libraries for evaluation, as it supports model formats and provides more features.

For more details on different model formats refer to the [CNTK model format](./CNTK-model-format.md) page.
For legacy applications that use use EvalDLL interface please refer to the [CNTK EvalDLL Overview](./EvalDll-Evaluation-Overview.md) page.
