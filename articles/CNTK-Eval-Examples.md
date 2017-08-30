---
title:   Model evaluation examples
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:  07/31/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   csharp, cpp
---

# Model evaluation examples

Examples for using the CNTK Eval library in C++, C#/.NET and Python can be found in [Examples/Evaluation](https://github.com/Microsoft/CNTK/blob/release/2.1/Examples/Evaluation) folder in GitHub, as well as in the CNTK binary download package on the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases).

## CNTK Library Eval C++/C# Examples
The [CNTKLibraryEvalExamples](https://github.com/Microsoft/CNTK/blob/release/2.1/Examples/Evaluation/CNTKLibraryEvalExamples.sln) contains code samples demonstrating how to use the CNTK Library Eval API in C++ and C#. 
- [**CNTKLibraryCSEvalCPUOnlyExamples**](https://github.com/Microsoft/CNTK/blob/release/2.1/Examples/Evaluation/CNTKLibraryCSEvalCPUOnlyExamples) uses the [CNTK Library CPU-Only NuGet package](https://www.nuget.org/packages/CNTK.CPUOnly) to evaluate models on CPU-only devices in C#. 

- [**CNTKLibraryCSEvalGPUExamples**](https://github.com/Microsoft/CNTK/blob/release/2.1/Examples/Evaluation/CNTKLibraryCSEvalGPUExamples) uses the [CNTK Library GPU NuGet package](https://www.nuget.org/packages/CNTK.GPU) to evaluate models on devices with Nvidia GPU in C#. 
- [**CNTKLibraryCPPEvalCPUOnlyExamples**](https://github.com/Microsoft/CNTK/blob/release/2.1/Examples/Evaluation/CNTKLibraryCPPEvalCPUOnlyExamples) uses the CNTK Library C++ API to evaluate models on CPU-only devices. It uses the [CNTK Library CPU-Only NuGet package](https://www.nuget.org/packages/CNTK.CPUOnly).
- [**CNTKLibraryCPPEvalGPUExamples**](https://github.com/Microsoft/CNTK/blob/release/2.1/Examples/Evaluation/CNTKLibraryCPPEvalGPUExamples) uses the CNTK Library C++ API to evaluate models on devices with Nvidia GPU. It uses the [CNTK Library GPU NuGet package](https://www.nuget.org/packages/CNTK.GPU).

- [**UWPImageRecognition**](https://github.com/Microsoft/CNTK/blob/release/2.1/Examples/Evaluation/UWPImageRecognition) contains an example using CNTK UWP library for model evaluation. It also shows how to use the library in C# via a C++/CX wrapper.

On Windows,
- You need Visual Studio 2015 update 3 for using these samples.
- The samples should be built for the 64-bit target platform. Otherwise some issues arise when calling the library. Please also refer to the [Troubleshoot CNTK](./Troubleshoot-CNTK.md) page for more information.
- After a successful build, the executable is saved under the $(SolutionDir)..\..\$(Platform)\$(ProjectName).$(Configuration)\ folder, e.g. ..\..\X64\CNTKLibraryCSEvalCPUOnlyExamples.Release\CNTKLibraryCSEvalCPUOnlyExamples.exe.
 
On Linux, only C++ is supported. Please refer to the `Makefile` for building samples. The target name CNTKLIBRARY_CPP_EVAL_EXAMPLES is used to build CNTKLibraryCPPEvalExamples.
 
### Examples for evaluating multiple requests in parallel
The [CNTKLibraryEvalExamples](https://github.com/Microsoft/CNTK/blob/release/2.1/Examples/Evaluation/CNTKLibraryEvalExamples.sln) includes sample code to evaluate multiple evaluation requests in parallel. The sample code also shows how to share model parameters among multiple instances of the same model to reduce memory usage.
- [**EvaluateMultipleImagesInParallelAsync()**](https://github.com/Microsoft/CNTK/blob/release/2.1/Examples/Evaluation/CNTKLibraryCSEvalCPUOnlyExamples/CNTKLibraryCSEvalExamples.cs) in C# demonstrates how to evaluate concurrent requests using CNTK C#/.NET Managed API.
- [**ParallelEvaluationExample()**](https://github.com/Microsoft/CNTK/blob/release/2.1/Examples/Evaluation/CNTKLibraryCPPEvalCPUOnlyExamples/CNTKLibraryCPPEvalCPUOnlyExamples.cpp) is the C++ example showing concurrent evaluation using CNTK C++ Library API.

### Examples for evaluating intermediate layers
- [**EvaluateIntermediateLayer()**](https://github.com/Microsoft/CNTK/blob/release/2.1/Examples/Evaluation/CNTKLibraryCSEvalCPUOnlyExamples/CNTKLibraryCSEvalExamples.cs) in C# demonstrates how to evaluate intermediate layers in a model using CNTK C#/.NET Managed API.
- [**EvaluateIntermediateLayer()**](https://github.com/Microsoft/CNTK/blob/release/2.1/Examples/Evaluation/CNTKLibraryCPPEvalCPUOnlyExamples/CNTKLibraryCPPEvalCPUOnlyExamples.cpp) is the C++ example showing how to evaluate intermediate layers in a model using CNTK C++ Library API.

### Examples for evaluating outputs from multiple nodes
- [**EvaluateCombinedOutputs()**](https://github.com/Microsoft/CNTK/blob/release/2.1/Examples/Evaluation/CNTKLibraryCSEvalCPUOnlyExamples/CNTKLibraryCSEvalExamples.cs) in C# demonstrates how to evaluate multiple outputs using CNTK C#/.NET Managed API.
- [**EvaluateCombinedOutputs()**](https://github.com/Microsoft/CNTK/blob/release/2.1/Examples/Evaluation/CNTKLibraryCPPEvalCPUOnlyExamples/CNTKLibraryCPPEvalCPUOnlyExamples.cpp) is the C++ example showing how to evaluate multiple outputs using CNTK C++ Library API.

### C# Examples for running evaluation asynchronously
The example [`EvaluationSingleImageAsync()`](https://github.com/Microsoft/CNTK/blob/release/2.1/Examples/Evaluation/CNTKLibraryCSEvalCPUOnlyExamples/CNTKLibraryCSEvalExamples.cs) demonstrates how to run evaluation asynchronously by using the extension method [`EvaluateAsync()`](https://github.com/Microsoft/CNTK/blob/release/2.1/Examples/Evaluation/CNTKLibraryCSEvalCPUOnlyExamples/CNTKExtensions.cs).

## Python Eval Examples
You can also use Python to evaluate a pre-trained model as described [here](./How-do-I-Evaluate-models-in-Python.md).

## Java Eval Examples
The [Java example](https://github.com/Microsoft/CNTK/blob/release/2.1/Tests/EndToEndTests/EvalClientTests/JavaEvalTest) shows how to evaluate a model using the Java API. Please note that the Java API is still experimental and subject to change.

Please refer to [Windows](./CNTK-Library-Evaluation-on-Windows.md#using-java) and [Linux](./CNTK-Library-Evaluation-on-Linux.md#using-java) instructions for how to build the Java example.

## Examples for legacy applications using EvalDLL

Please refer to [EvalDll-Examples](./Archive/EvalDll-Examples.md) for details.
