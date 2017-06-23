---
title:   EvalDll C++/C# Examples
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   06/22/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript,cpp, csharp,dotnet,python
---

# EvalDll C++/C# Examples

Prior to the CNTK 2.0 version, the CNTK EvalDLL wa used to evaluate model trained by using cntk.exe with BrainScript. The EvalDLL 
is still supported, but works only for the model created by cntk.exe with BrainScript. It can not be used to evaluate models created 
by CNTK 2.0 using Python. We strongly recommend to use the CNTK 2.0 Libraries for evaluation, as it supports model formats and provides more features.

For legacy applications that are still using EvalDll, The [EvalClients.sln](https://github.com/Microsoft/CNTK/blob/v2.0/Examples/Evaluation/EvalClients.sln) contains the following examples: 
- [`CPPEvalClient`](https://github.com/Microsoft/CNTK/blob/v2.0/Examples/Evaluation/CPPEvalClient): this sample uses the C++ `EvalDll`.
- [`CPPEvalExtendedClient`](https://github.com/Microsoft/CNTK/blob/v2.0/Examples/Evaluation/CPPEvalExtendedClient): this sample uses the C++ extended Eval interface in `EvalDll` to evaluate a RNN model. 
- [`CSEvalClient`](https://github.com/Microsoft/CNTK/blob/v2.0/Examples/Evaluation/CSEvalClient): this sample uses the C# `EvalDll` (only for Windows). It uses the [CNTK EvalDll Nuget Package](https://www.nuget.org/packages/Microsoft.Research.CNTK.CpuEval-mkl/). 
 
On Windows, The solution file [EvalClients.sln](https://github.com/Microsoft/CNTK/blob/v2.0/Examples/Evaluation/EvalClients.sln) is used to build and run samples. Please note 
- You need Visual Studio 2015 update 3 for using these samples.
- The samples should be built for the 64-bit target platform. Otherwise some issues arise when calling the library. Please also refer to the [Troubleshoot CNTK](./Troubleshoot-CNTK.md) page for more information.
- After a successful build, the executable is saved under the $(SolutionDir)..\..\$(Platform)\$(ProjectName).$(Configuration)\ folder, e.g. ..\..\X64\CPPEvalClient.Release\CppEvalClient.exe. 

On Linux, please refer to the `Makefile` for building samples. The target name EVAL_CLIENT, and EVAL_EXTENDED_CLIENT are used to build these projects. 
