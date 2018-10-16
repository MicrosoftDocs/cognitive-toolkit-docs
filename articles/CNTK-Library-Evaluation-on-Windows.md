---
title:   Model Evaluation on Windows
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:  07/31/2017
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   csharp, cpp
---

# Model Evaluation on Windows

Currently, CNTK provides libraries in C++, C#/.NET, Python and Java for loading and evaluating models on Windows. Starting from v2.1, CNTK also supports Universal Windows Platform (UWP).

## Using C#/.NET Managed API
The CNTK Library provides a managed (.NET) library for evaluation of CNTK models on CPU and GPU using C# and other .NET languages. The NuGet package CNTK.CPUOnly is designed for execution on CPU, and the CNTK.GPU is designed for execution on devices with NVIDIA GPU. Please refer to the [NuGet Package](./NuGet-Package.md) page about details how to install CNTK Library NuGet Packages. Please note that Visual Studio 2017 is required and the target platform in the project property should be X64.

The CNTK Library Managed API allows you to load and evaluate pre-trained models on specified devices (CPU or GPU). It also provides different ways to help you to prepare input and output data in the dense, one-hot vector or sparse CSC format. The input data can be a single sample, batch of multiple samples, a sequence of samples, or a batch of sequences with variable lengths. A detailed description about batch and sequence format can be found at the page [CNTK Concepts](https://cntk.ai/pythondocs/sequence.html#cntk-concepts). After evaluation, the API provides convenient methods to get results.

### C#/.NET Programming Guide
The usual steps for model evaluation using CNTK Library Managed API include:
  1. Install the appropriate NuGet package (CPU or GPU depending on usage) to your. Details are described in the [CNTK NuGet Package](./NuGet-Package.md).
  2. Load the model by [Function.Load()](./CNTK-Library-Managed-API.md#class-function). The returned value is a [`Function`](./CNTK-Library-Managed-API.md#class-function) instance referring to the loaded model.
  3. If needed, get inputs and outputs of the loaded model by calling the properties [`Arguments`](./CNTK-Library-Managed-API.md#class-function), [`Output`](./CNTK-Library-Managed-API.md#class-function) or [`Outputs`](./CNTK-Library-Managed-API.md#class-function) of the Function instance returned by Load(). The inputs and outputs are represented as [`Variable`](./CNTK-Library-Managed-API.md#class-variable). Further information about each individual input or output, e.g. [`Name`](./CNTK-Library-Managed-API.md#class-variable) or [`Shape`](./CNTK-Library-Managed-API.md#class-variable) can be found by calling corresponding properties of the Variable.
  4. For each input, prepare input data for evaluation. Depending on usage, input data might consist of a single sample, batch of multiple samples, a sequence of samples, or a batch of sequences with variable length. Regarding storage, input data can be in the dense format, one-hot vector, or sparse CSC format. The [`Value`](./CNTK-Library-Managed-API.md#class-value) object is used to represent the input data with different contents and formats. The CNTK API provides different methods to easily create the Value object from plain user data.
     * [`CreateBatch()`](./CNTK-Library-Managed-API.md#class-value) creates a Value object containing a batch of single or multiple samples.
     * [`CreateSequence()`](./CNTK-Library-Managed-API.md#class-value) creates a Value object containing a sequence with multiple samples. It is possible to specify whether this sequence is a new sequence or continuation of a previous sequence at the same index in the sequences list from a previous call to this method.
     * [`CreateBatchOfSequences()`](./CNTK-Library-Managed-API.md#class-value) creates a Value object containing a batch of sequences. Each sequence has a variable length of samples.
     All the methods above support both the dense, one-hot vector or sparse CSC format.
  5. Call the [`Evaluate()`](./CNTK-Library-Managed-API.md#class-function) method to execute evaluation. The parameter `arguments` defines input Variables and their associated Values, and `outputs` defines output Variables. If the Value object of a output variable is `null`, CNTK Library allocates the actual storage for the output Value. On return, Evaluate() saves the evaluation results into the Value objects associated the output variables.
  6. Call the [`GetDenseData()/GetOneHotData()`](./CNTK-Library-Managed-API.md#class-value) method of the output Value to get the results in plain data format. It copies the data stored in the Value object into the buffer as a list of sequences with variable length samples, either in dense format or one-hot vector format (support for the sparse format will be added soon).

The CNTK Library Managed API is described in the [CNTK Library C#/.NET Managed API](./CNTK-Library-Managed-API.md) page.

### Evaluation of multiple requests in parallel
CNTK supports evaluating multiple requests in parallel. Because running evaluation on the same model instance is not thread-safe, it is required first to create multiple model instances by calling Clone() with `ParameterCloningMethod.Share`, and then each thread uses a separate model instance for evaluation. The [`EvaluateMultipleImagesInParallelAsync()`](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Evaluation/CNTKLibraryCSEvalCPUOnlyExamples/CNTKLibraryCSEvalExamples.cs) demonstrates how to evaluate concurrent requests using CNTK C#/.NET Managed API.

### Run evaluation asynchronously
CNTK C# API does not have an asynchronous method for Evaluate(), because the evaluation is a CPU-bound operation (Please refer to [this article] (https://blogs.msdn.microsoft.com/pfxteam/2012/03/24/should-i-expose-asynchronous-wrappers-for-synchronous-methods/) for detailed explanation). However, it is desired to run evaluation asynchronously in some use cases, e.g. offloading for responsiveness, we show in the example [`EvaluationSingleImageAsync()`](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Evaluation/CNTKLibraryCSEvalCPUOnlyExamples/CNTKLibraryCSEvalExamples.cs) how to achieve that by using the extension method [`EvaluateAsync()`](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Evaluation/CNTKLibraryCSEvalCPUOnlyExamples/CNTKExtensions.cs).

### Evaluate intermediate layers
CNTK C# API is able to evaluate an intermediate layer in a model graph. To that end, the desired layer can be found by its name, and the intermediate layer can be evaluated. The example [**EvaluateIntermediateLayer()**](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Evaluation/CNTKLibraryCSEvalCPUOnlyExamples/CNTKLibraryCSEvalExamples.cs) in C# demonstrates how to evaluate an intermediate layer in a model.

### Evaluate outputs from multiple nodes
CNTK can evaluate multiple nodes in a model graph by combining their outputs into a single node. The example [**EvaluateCombinedOutputs()**](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Evaluation/CNTKLibraryCSEvalCPUOnlyExamples/CNTKLibraryCSEvalExamples.cs) in C# demonstrates how to evaluate multiple outputs.

### C# Examples
The [CNTKLibraryCSEvalExamples](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Evaluation/CNTKLibraryCSEvalCPUOnlyExamples/CNTKLibraryCSEvalExamples.cs) shows how to evaluate a model in C# using CNTK Library NuGet packages. Please see the [Eval Examples](./CNTK-Eval-Examples.md) page for building and running examples.

If you do not want to use NuGet Package, you can add `Cntk.Core.Managed-<VERSION>.dll` as reference to your project. The `Cntk.Core.Managed` DLL and all dependent DLLs can be found in the CNTK binary release package on the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases). Please make sure that the path to `Cntk.Core.Managed` DLL and its [dependencies](./CNTK-Library-Evaluation-on-Windows.md#shipping-cntk-library-with-your-windows-application) (see list at the end of this page) are included in the search path of DLLs for your application.

## Using C++
The CNTK Library C++ API supports model evaluation in C++ applications. Both standard desktop applications and Universal Windows Platform applications are supported. Visual Studio 2017 is required and the target platform in the project property should be X64.

The CNTK NuGet package CNTK.CPUOnly and CNTK.GPU contain C++ CNTK Library for using CNTK in desktop applications. For using CNTK on Universal Windows Platform, please install CNTK.UWP.CPUOnly. By using NuGet packages, configuration about include and library directories is automatically added to your project property. Both Debug and Release build are supported. The [NuGet Package](./NuGet-Package.md) page explains how to get and install CNTK NuGet Packages.

### C++ Programming Guide
The following steps describe how to use the C++ CNTK Library for model evaluation.   

1. Link the `Cntk.Core-<VERSION>.lib` import library into the application. This step can be omitted if you use the NuGet package. For UWP application, the library name is `Cnkt.Core_app-<Version>.lib`.
2. Include the evaluation header file "CNTKLibrary.h".
3. Load the model by `CNTK::Function::Load()`. The returned Function object represents the computation graph of the model.
4. Prepare data for each input variable. You can use `CNTK::Value::CreateBatch()`, `CNTK::Value::CreateSequence()`, or `CNTK::Value::CreateBatchOfSequences()` to create a Value object from your input data representing a batch of samples, a sequence of samples, or a batch of sequences of samples respectively.
5. Call `CNTK::Function::Evaluate()` for evaluation. The `Evaluate()` expects as input an unordered_map for input variables and input values, and an unordered_map for output variables and output values. The output value object could be `null` which means that CNTK Library allocates the actual storage for this output value. On return, the Value objects associated with the output variables contain the results of evaluation.
6. Get output data from evaluation results. Use `CNTK::Value::CopyVariableValueTo()` to copy the data stored in the Value object into the buffer as a list of sequences with variable length of samples.

For concurrent evaluation of multiple requests, `CNTK::Function::Clone()` should be called before Evaluate(). The [`ParallelEvaluationExample()`]( https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Evaluation/CNTKLibraryCPPEvalCPUOnlyExamples/CNTKLibraryCPPEvalCPUOnlyExamples.cpp) demonstrates how to evaluate multiple requests in parallel using CNTK C++ Library API.

For details on C++ CNTK Library API for evaluation, please refer to the [CNTK Library C++ Evaluation Interface](./CNTK-Library-Native-Eval-Interface.md) page.

### C++ Examples
The C++ examples [`CNTKLibraryCPPEvalCPUOnlyExamples`](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Evaluation/CNTKLibraryCPPEvalCPUOnlyExamples) and [`CNTKLibraryCPPEvalGPUExamples`](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Evaluation/CNTKLibraryCPPEvalGPUExamples) illustrate the usage pattern above. The [UWPImageRecognition](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Evaluation/UWPImageRecognition) contains an example using CNTK UWP library for model evaluation. The [Eval Examples](./CNTK-Eval-Examples.md) page provides detailed steps about building and running examples.

Alternatively, you can use C++ CNTK Library without the NuGet Package. You can either get `Cntk.Core-<VERSION>.lib` (or `Cntk.Core_app-<Version>.lib`) and all dependent DLLs from the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases) or build them from CNTK source code. You also need to configure include and library path to point to the correct directory, and make sure that the build configuration (Debug or Release) of the CNTK Library is the same as that of your project setting (Otherwise you will get unexpected exceptions). The CNTK release package contains only the release build of binaries.

## Shipping CNTK Library with your Windows application
CNTK Library requires the Visual C++ Redistributable Package for Visual Studio 2017 to be installed on the system where your application is going to run. And the target platform in the project property should be X64.

This [page](./CNTK-Shared-Libraries-Naming-Format.md) describes how CNTK binary files are named.

For desktop C++ applications, the following DLLs need to be distributed with your application. 
* `Cntk.Core-<VERSION>.dll`
* `Cntk.Math-<VERSION>.dll`
* `libiomp5md.dll`
* `mklml.dll`

If you use the C#/.NET Managed Library, beside the DLLs mentioned above, you need to additionally include the following DLLs:
* `Cntk.Core.Managed-<VERSION>.dll`
* `Cntk.Core.CSBinding-<VERSION>.dll`

For using GPU, you need in addition to include the following NVIDIA CUDA related DLLs:
* `cublas64_90.dll`
* `cudart64_90.dll`
* `cudnn64_7.dll`
* `curand64_90.dll`
* `cusparse64_90.dll`
* `nvml.dll`

> [!NOTE]
> [!INCLUDE[versionchanged-2.1](includes/versionchanged-2.1.md)]
> CNTK NuGet packages are shipping with cuDNN 6 (`cudnn64_6.dll`).

All these DLLs can be found in the CNTK binary release version, see the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases). 

For UWP applications, the following DLLs need to be distributed with your application. You can find these DLLs in the Nuget package `CNTK.UWP.CPUOnly`.
* `Cntk.Core_app-<VERSION>.dll`
* `Cntk.Math_app-<VERSION>.dll`
* `libopenblas.dll`

## Using Python
You can use Python to evaluate a pre-trained model. Examples can be found [here](./How-do-I-Evaluate-models-in-Python.md).

## Using Java
CNTK also provides APIs for evaluating model in Java application. Please note that the CNTK Java API is still experimental and subject to change.

The [Java example](https://github.com/Microsoft/CNTK/tree/release/latest/Tests/EndToEndTests/EvalClientTests/JavaEvalTest/src/Main.java) shows how to evaluate a CNN model using the Java API.

For using CNTK Java Library, please add the `cntk.jar` file to the `classpath` of your Java project. If you are working with an IDE you should add this as an unmanaged jar. The cntk.jar file can be found in the CNTK binary release package (in the folder cntk\cntk\java). You can also build cntk.jar from CNTK source. Please also set `java.library.path` to the directory containing `Cntk.Core.JavaBinding-<Version>.dll`, and make sure that the directory containing CNTK binary DLLs is in the DLL search path of your system, e.g. by adding the directory to the PATH environment variable. Please note that you also need to install Visual C++ Redistributable Package for Visual Studio 2017. If you get `UnsatisfiedLinkErrors` in Java, it is typically because that the directory is not in the DLL search path (or in the wrong order).

The Java library is currently built and tested with 64-bit Oracle JDK 8. 
