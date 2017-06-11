---
title:   CNTK Evaluation Overview
author:    chrisbasoglu
date:    04/10/2017
ms.author:   cbasoglu
ms.date:   04/10/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK Evaluation Overview

Once you have trained a model, you need the functionality to evaluate the model in your target environment. As a reminder, 
there are several ways to create models with CNTK, and there are two different formats to store the model in.

The original CNTK (prior to the CNTK 2.0 version) only supports a format we call now the model-V1 format. It was originally created by using CNTK.EXE with BrainScript. With CNTK 2.0 a Protobuf based format was introduced, which is now 
known as the model-V2 format. The following table presents an overview on creating the different model formats:

|  _model-creation_     | model-v1 | model-v2 |
|:----------------------|:---------|:---------|
| CNTK.EXE (BrainScript) |     YES     | NO |
| CNTK-library (Python, C++) | deprecated | YES |

For more details on creating the different model formats refer to the [CNTK model format](./CNTK-model-format.md) page.

## CNTK model evaluation methods

Aside from training a model, Microsoft Cognitive Toolkit provides different ways of evaluating the model:

|  _model-evaluation_     | features | model-v1 | model-v2 |
|:----------------------|:----|:----|:-----|
| [CNTK.EXE](./CNTK-Evaluation-using-cntk.exe.md) | BrainScript |    YES     | NO |
| | GPU and CPU support |||
| [CNTK-EvalDLL](./EvalDll-Evaluation-Overview.md) |  C++, C#/.NET | YES | NO |
| | ASP and Azure support |||
| | Nuget Package for CPU-Only support |||
|[CNTK-library](./CNTK-Library-Evaluation-Overview.md) | C++, C#/.NET, Java and Python | YES | YES | 
| | ASP and Azure support |||
| | GPU and CPU |||
| | Nuget Packages |||
| | Support multiple parallel requests |||
| | Optimized memory usage by parameter sharing ||| 

The following pages provide detailed information about model evaluation in different scenarios:

- [CNTK-library evaluation on Windows](./CNTK-Library-Evaluation-on-Windows.md)
- [CNTK-library evaluation on Linux](./CNTK-Library-Evaluation-on-Linux.md)
- [CNTK-library evaluation with Python](./How-do-I-Evaluate-models-in-Python.md)

***

- [Evaluation using NuGet-Packages](./NuGet-Package.md)
- [Evaluation in Azure](./Evaluate-a-model-in-an-Azure-WebApi.md)

***

- [EvalDll evaluation on Windows](./EvalDll-Evaluation-on-Windows.md)
- [EvalDll evaluation on Linux](./EvalDll-Evaluation-on-Linux.md)    
- [Evaluating a model using CNTK.EXE](./CNTK-Evaluation-using-cntk.exe.md)    
