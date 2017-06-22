---
title:   CNTK Library Evaluation Overview
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   06/01/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK Library Evaluation Overview

The [CNTK Library API](./CNTK-Library-API.md) allows to evaluate both CNTK model-v1 and model-V2 [format](./CNTK-model-format.md). The CNTK Library can be consumed on Windows and Linux by C++, Python, C#, Java and other .NET languages. 

New features of the CNTK Library for Evaluation include
* Support both CPU and GPU device.
* [Support multiple evaluation requests in parallel](./CNTK-Eval-Examples.md#examples-for-evaluating-multiple-requests-in-parallel).
* Optimize memory usage by parameter sharing of the same model between multiple threads. This will significantly reduce memory usage when running evaluation in a service environment. 

The following pages provide detailed information about model evaluation using CNTK Library.
* [CNTK-library evaluation on Windows](./CNTK-Library-Evaluation-on-Windows.md)
* [CNTK-library evaluation on Linux](./CNTK-Library-Evaluation-on-Linux.md)
* [CNTK-library evaluation with Python](./How-do-I-Evaluate-models-in-Python.md)
* [NuGet-Packages](./NuGet-Package.md)
* [Evaluation in Azure](./Evaluate-a-model-in-an-Azure-WebApi.md)

