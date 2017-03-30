The CNTK library on Windows is currently available in C++, C#/.NET and Python. 

## Using the CNTK Library Managed API 
The CNTK Library Managed Eval API provides a managed (.NET) library for evaluation of CNTK models on CPU and GPU using C# and other .NET languages. The Nuget package CNTK.CPUOnly is for evaluation on CPU, and the CNTK.GPU is for evalution on devices with NVIDIA GPU.  These Nuget packages are available at Nuget.org (search for CNTK). Please refer to the [NuGet Package](./NuGet-Package) page for details on how to get started with CNTK Library NuGet Packages. 

The CNTK Library Managed Eval API allows you to load pre-trained models onto the specified device (CPU or GPU), retrieve model inputs and outputs information, prepare your input data for evaluation. It supports both dense and one-hot vector format data. The input data can be a single sample, batch of multiple samples, a sequence of samples, or a batch of sequences with variable lengeth. A detailed description about batch and sequence format can be found at the page [CNTK Concepts](https://www.cntk.ai/pythondocs/sequence.html#cntk-concepts). After evaluation, the API provides convenient methods to get results, either in the dense or the one-hot vector format (The support for spase format will be avaialble soon).

The usual steps for model evaluation using CNTK Library Managed API include:     
  1. Install the appripriate Nuget package (CPU or GPU depending on usage) to your. Details are described in the [CNTK NuGet Package](./NuGet-Package).    
  2. Load the model by calling [Function.LoadModel()](./CNTK-Library-Managed-API#class-function). The returned value is a [`Function`](./CNTK-Library-Managed-API#class-function) instance referring to the loaded model.   
  3. If neded, get inputs and outputs of the loaded model by calling the properties [`Arguments`](./CNTK-Library-Managed-API#class-function), [`Output`](./CNTK-Library-Managed-API#class-function) or [`Outputs`](./CNTK-Library-Managed-API#class-function) of the Function instance returned by LoadModel(). The inputs and outputs are represented as [`Variable`](./CNTK-Library-Managed-API#class-variable). Further information about each individual inputs or outputs, e.g. [`Name`](./CNTK-Library-Managed-API#class-variable) or [`Shape`](./CNTK-Library-Managed-API#class-variable) can be found by calling corresponding properties of Variable.   
  4. For each input, prepare input data for evaluation. Depending on usage, input data might consist of a single sample, batch of multiple samples, a sequence of samples, or a batch of sequences with variable legnth. Regarding storage, input data can be in the dense format, or in the one-hot vector format (the sparse format will be added soon). The [`Value`](./CNTK-Library-Managed-API#class-value) object is used to represent the input data with different contents and formats. There are different methods provided to easily create the Value object from plain user data.
     * [`CreateBatch()`](./CNTK-Library-Managed-API#class-value) creates a Value object containing a batch of single or multiple samples.
     * [`CreateSequence()`](./CNTK-Library-Managed-API#class-value) creates a Value object containing a sequence with multiple samples. It is possible to specify whether this sequence is a new sequence or continuation of a previous sequence at the same index in the sequences list from a previous call to this method.
     * [`CreateBatchOfSequences()`](./CNTK-Library-Managed-API#class-value) creates a Value object containting a batch of sequences. Each sequence consists of variable length samples. 
     All the methods above support both the dense and the one-hot vector format as input.
  5. Call the [`Evaluate()`](./CNTK-Library-Managed-API#class-function) method of the Function instance returned by LoadModel() to start model evaluation. Besides the input Variables and their assoicated Values, Evaluate() also requires to specify the output Variables and possible Values. The caller may explicitly pass the Value object for a output variable or pass `null` in which case CNTK Library allocates the actual storage for the output value. On return the Value objects associated with the output variables contain the results of evaluation.
  6. Call the [`CopyVariableTo()`](./CNTK-Library-Managed-API#class-value) method of the output Value to get the results in plain data format. CopyVariableTo() copies the data stored in the Value object into the buffer as a list of sequences with variable length samples, either in the dense format or the one-hot vector format (support for the sparse format will be added soon). 

In order to evaluate multiple evaluation requests in parallel, `clone()` should be called to create multiple Function instances before Evaluate(). The [`EvaluateMultipleImagesInParallel()`](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryCSEvalCPUOnlyExamples/CNTKLibraryCSEvalExamples.cs) demonstrates how to evaluate concurrent requests using CNTK C#/.NET Managed API.

The CNTK Library Managed API is described in the [CNTK Library Managed API](./CNTK-Library-Managed-API) page.

The  [CNTKLibraryEvalExamples](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryEvalExamples.sln) shows how to evaluate a model in C# using CNTK Libarary Nuget packages. Please see the [Eval Examples](./CNTK-Eval-Examples) page for building and running examples.

If you do not want to use NuGet Package, you can add CNTKLibraryManaged-2.0.dll as reference to your project. The CNTKLibraryManaged-2.0.dll and all dependent dlls can be found in the CNTK binary release package on the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases). Please make sure in this case that the path to CNTKLibraryManaged-2.0.dll and its [dependencies](./CNTK-Library-Evaluation-on-Windows#shipping-CNTK-library-with-your-windows-application) are included in the search path of dlls for your application. 

## Using the C++ CNTK Library API for evaluation

The CNTK Nuget packages CNTK.CPUOnly and CNTK.GPU also contain C++ CNTK Library. Please refer to the [NuGet Package](./NuGet-Package) page for details on how to get CNTK NuGet Packages. Please note that C++ CNTK Library requires Visual Studio 2015 Update 3 and the target platform in the project property should be X64.

To use C++ CNTK Library in your project, you just need to install the appropriate Nuget package to your project. No addtional configuration of include and library directories is needed, as it is automatically added to your project property by the Nuget package. The Nuget package supports both Debug and Release build. 

The following steps describe how to use the C++ CNTK Library for model evaluation.   
       
1. Link the CNTKLibrary-2.0.lib import library into the application. This step can be omitted if you use the Nuget package.
2. Include the evaluation header file "CNTKLibrary.h". The header file is available under Include in the binary release pacakge.    
3. Load the model by `CNTK::Function::LoadModel()`. The returned Function object represents the computation graph of the model.
4. Prepare data for each input variable. You can use `CNTK::Value::CreateBatch()`, `CNTK::Value::CreateSequence()`, or `CNTK::Value::CreateBatchOfSequences()` to create a Value object from your input data representing a batch of samples, a sequence of samples, and a batch of sequences of samples respectively. 
5. Call `CNTK::Function::Evaluate()` for evaluation. The `Evaluate()` expects as input an unordered_map for input variables and input values, and an unordered_map for output variables and output values. The output value object could be `null` which means that CNTK Library allocates the actual storage for this output value. On return the Value objects associated with the output variables contain the results of evaluation. 
6. Get output data from evaluation results. Use `CNTK::Value::CopyVariableTo()` to copy the data stored in the Value object into the buffer as a list of sequences with variable length samples.

For concurrent evaluation of multiple requests, `CNTK::Function::Clone()` should be called before Evaluate(). The [`MultiThreadsEvaluationWithLoadModel()`]( https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryCPPEvalCPUOnlyExamples/EvalMultithreads.cpp) demonstrates how to evaluate multiple requests in parallel using CNTK C++ Library API.

The C++ examples [`CNTKLibraryCPPEvalCPUOnlyExamples`](https://github.com/Microsoft/CNTK/tree/master/Examples/Evaluation/CNTKLibraryCPPEvalCPUOnlyExamples) and [`CNTKLibraryCPPEvalGPUExamples`](https://github.com/Microsoft/CNTK/tree/master/Examples/Evaluation/CNTKLibraryCPPEvalGPUExamples) illustrate the usage pattern above. The [Eval Examples](./CNTK-Eval-Examples) page provides detailed steps about building and running examples.

For details on C++ CNTK Library API for evaluation, please refer to the [CNTK Library Evaluation Interface](./Native-CNTK-Library-Eval-Interface) page in this wiki.

Alternatively, you can use C++ CNTK Library without the NuGet Package. You can either get CNTKLibrary-2.0.lib and all dependent dlls from CNTK release packages on the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases) or build them from CNTK source code. In this case, you need to configure include and library path to point to the correct directory, and make sure that the build configuration (Debug or Release) of the CNTK Library is the same as that of your project setting (Otherwise you will get unexpected exceptions). The CNTK release package contains only the release build of binaries. Please also note that CNTK Library requires 64bit platform, and the path to CNTKLibrary-2.0.dll and its [dependencies](./CNTK-Library-Evaluation-on-Windows#shipping-CNTK-library-with-your-windows-application) should be in the search path of dlls for your application.

## Shipping CNTK Library with your Windows application
If you own application uses CNTK Library C++ API you need to distribute these DLLs with your application:
* CNTKLibrary-2.0.dll
* Math.dll
* libiomp5md.dll
* mkl_cntk_p.dll

If you use the Managed Library, beside the dlls mentioned above, you need to additionally include the following DLLs:
* CNTKLibraryManaged-2.0.dll
* CNTKLibraryCSBinding.dll

For using GPU, you need in addtion to include the following NVIDIA CUDA releated DLLs:
* cublas64_80.dll
* cudart64_80.dll
* cudnn64_5.dll
* curand64_80.dll
* cusparse64_80.dll

All these dlls can be found in the CNTK binary release version, see the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases).

## Using Python
You can use Python to evaluate a pre-trained model. Examples can be found [here](./Evaluate-a-saved-convolutional-network).