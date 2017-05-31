---
title:   Model eval examples
author:    chrisbasoglu
date:    04/09/2017
ms.author:   cbasoglu
ms.date:   04/09/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   csharp, cpp
---

# Model eval examples

## Eval samples in CNTK binary download package 
The CNTK binary download package on the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases) includes samples for using the eval library in C++, C#/.NET and Python.

## CNTK Library Eval C++/C# Examples
The [CNTKLibraryEvalExamples](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryEvalExamples.sln) contains code samples demonstrating how to use the CNTK Library Eval API in C++ and C#. 
- [`CNTKLibraryCSEvalCPUOnlyExamples`](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryCSEvalCPUOnlyExamples) uses the [CNTK Library CPU-Only Nuget package](https://www.nuget.org/packages/CNTK.CPUOnly) to evaluate models on CPU-only devices in C#. 

- [`CNTKLibraryCSEvalGPUExamples`](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryCSEvalGPUExamples) uses the [CNTK Library GPU Nuget package](https://www.nuget.org/packages/CNTK.GPU) to evaluate models on GPU devices in C#. 
- [`CNTKLibraryCPPEvalCPUOnlyExamples`](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryCPPEvalCPUOnlyExamples) uses the CNTK Library C++ API to evaluate models on CPU-only devices. It uses the [CNTK Library CPU-Only Nuget package](https://www.nuget.org/packages/CNTK.CPUOnly).
- [`CNTKLibraryCPPEvalGPUExamples`](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryCPPEvalGPUExamples) uses the CNTK Library C++ API to evaluate models on GPU devices. It uses the [CNTK Library GPU Nuget package](https://www.nuget.org/packages/CNTK.GPU).

On Windows, 
- You need Visual Studio 2015 update 3 for using these samples.
- The samples should be built for the 64-bit target platform. Otherwise some issues arise when calling the library. Please also refer to the [Troubleshoot CNTK](./Troubleshoot-CNTK.md) page for more information.
- After a successful build, the executable is saved under the $(SolutionDir)..\..\$(Platform)\$(ProjectName).$(Configuration)\ folder, e.g. ..\..\X64\CNTKLibraryCSEvalCPUOnlyExamples.Release\CNTKLibraryCSEvalCPUOnlyExamples.exe.
 
On Linux, only C++ is supported. Please refer to Makefile for building samples. The target name CNTKLIBRARY_CPP_EVAL_EXAMPLES is used to build CNTKLibraryCPPEvalExamples. 
 
## Examples for evaluating multiple requests in parallel
The [CNTKLibraryEvalExamples](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryEvalExamples.sln) includes sample code to evaluate multiple evaluation requests in parallel. The sample code also shows how to share model parameters among multiple instances of the same model to reduce memory usage.
- [`EvaluateMultipleImagesInParallel()`](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryCSEvalCPUOnlyExamples/CNTKLibraryCSEvalExamples.cs) in C# demonstrates how to evaluate concurrent requests using CNTK C#/.NET Managed API.
- The [`MultiThreadsEvaluationWithLoadModel()`]( https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryCPPEvalCPUOnlyExamples/EvalMultithreads.cpp) is the C++ example showing concurrent evaluation using CNTK C++ Library API.

## EvalDll Eval C++/C# Examples
The [EvalClients.sln](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/EvalClients.sln) contains the following projects demonstrating how to use the `EvalDll` library in C++ and C#.
- [`CPPEvalClient`](https://github.com/Microsoft/CNTK/tree/master/Examples/Evaluation/CPPEvalClient): this sample uses the C++ `EvalDll`.
- [`CPPEvalExtendedClient`](https://github.com/Microsoft/CNTK/tree/master/Examples/Evaluation/CPPEvalExtendedClient): this sample uses the C++ extended Eval interface in `EvalDll` to evaluate a RNN model. 
- [`CSEvalClient`](https://github.com/Microsoft/CNTK/tree/master/Examples/Evaluation/CSEvalClient): this sample uses the C# `EvalDll` (only for Windows). It uses the [CNTK EvalDll Nuget Package](https://www.nuget.org/packages/Microsoft.Research.CNTK.CpuEval-mkl/). 
 
On Windows, The solution file [EvalClients.sln](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/EvalClients.sln) is used to build and run samples. Please note 
- You need Visual Studio 2015 update 3 for using these samples.
- The samples should be built for the 64-bit target platform. Otherwise some issues arise when calling the library. Please also refer to the [Troubleshoot CNTK](./Troubleshoot-CNTK.md) page for more information.
- After a successful build, the executable is saved under the $(SolutionDir)..\..\$(Platform)\$(ProjectName).$(Configuration)\ folder, e.g. ..\..\X64\CPPEvalClient.Release\CppEvalClient.exe. 

On Linux, please refer to Makefile for building samples. The target name EVAL_CLIENT, and EVAL_EXTENDED_CLIENT are used to build these projects. 
 
## Python Eval Examples
You can also use Python to evaluate a pre-trained model as described [here](./How-do-I-Evaluate-models-in-Python.md).

## CNTK Library Eval Java Examples
The [Java example](https://github.com/Microsoft/CNTK/tree/master/Tests/EndToEndTests/EvalClientTests/JavaEvalTest) shows how to evaluate a model using the Java API. Please note that the Java API is still experimental and subject to change.

Please refer to [Windows](./CNTK-Library-Evaluation-on-Windows#using-java) and [Linux](./CNTK-Library-Evaluation-on-Linux#using-java) instructions for how to build the Java example.
