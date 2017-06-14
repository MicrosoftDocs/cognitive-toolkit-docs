---
title:   CNTK Library API
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   06/01/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   python, cpp, csharp
---

# CNTK Library API

From C++ and Java you can access the CNTK model evaluation facilities. CNTK core computational, neural network composition & training, as well as model evaluation facilities are exposed through Python and C++.

## Python API

[CNTK Python API](https://cntk.ai/pythondocs/) consists of abstractions for model definition and compute, learning algorithms, data reading and distributed training. 
- *Flexibility and compactness*: These abstractions orthogonally compose offering both flexibility and conciseness in definition and training of arbitrary neural networks. 
- *Efficient data interfaces*: Simple yet light-weight data interfaces allow users to efficiently feed data in the form of native numpy arrays to the compute engine. 
- *Built-in data readers*: CNTK's built-in optimized and scalable data readers for image, text format and speech HTK data formats are also available from the Python APIs for ease of directly training with existing data without users having to author any data reading code.
- *Highly scalable learning*: The API exposes CNTK's highly scalable distributed training capabilities (parallelization algorithms like 1-Bit SGD). The [distributed training example](https://github.com/Microsoft/CNTK/tree/v2.0/Examples/Image/Classification/ResNet/Python#trainresnet_cifar10_distributedpy) illustrates the training parallelization API.
- *Concise network definition*: The API includes a high level [layers library](https://cntk.ai/pythondocs/layerref.html) that enables concise advanced neural networks definition including recurrences similar to CNTK V1. The toolkit supports representation of recurrent models in symbolic form as cycles in the neural network instead of requiring static unrolling of the recurrence steps. This results in much more general, concise and efficient representation and execution of recurrent neural networks. 

All the core computation, learning, and data reading API abstractions in the CNTK Python API are very easily extensible from both Python and C++ allowing users to easily implement new operators, learners and data readers which freely compose with the built-in facilities of the library.

The API introduces new [Protocol Buffers](https://developers.google.com/protocol-buffers/) based model serialization format which supports backwards and upwards compatibility for saved models.

## C++ API

The CNTK Library C++ API exposes CNTK's core computational, neural network composition & training, efficient data reading, and scalable model training facilities for developers. The C++ APIs are fully featured for both model training as well as evaluation, allowing for both training and model serving to be driven from native code. This enables your native code to tune the online model using new data as part of a evaluation request (i.e. online learning).

The examples [`CNTKLibraryCPPEvalCPUOnlyExamples`](https://github.com/Microsoft/CNTK/tree/master/Examples/Evaluation/CNTKLibraryCPPEvalCPUOnlyExamples) and [`CNTKLibraryCPPEvalGPUExamples`](https://github.com/Microsoft/CNTK/tree/master/Examples/Evaluation/CNTKLibraryCPPEvalGPUExamples) illustrate the use of the C++ APIs for evaluating previously trained CNTK models. We are also actively working on creating C++ examples illustrating how model training can be performed using the native C++ API and these will become available in a few weeks. 

Currently the best source of API documentation is inline in the API header file ([CNTKLibrary.h](https://github.com/Microsoft/CNTK/blob/master/Source/CNTKv2LibraryDll/API/CNTKLibrary.h)) that contains the full C++ API definition. The API header files are also included in the binary release package under the Include directory.

## C#/.NET Managed API

The CNTK Library Managed API exposes Evaluation related APIs for developers using C# or other .NET languages. It is provided as Nuget packages. The Nuget package CNTK.CPUOnly is for evaluation on CPU, and the CNTK.GPU is for evaluation on devices with NVIDIA GPU.

For details regarding the CNTK Library managed API, please refer to the [CNTK Library Managed API](./CNTK-Library-Managed-API.md) page.

There are several [Eval Examples](./CNTK-Eval-Examples.md) inside the [CNTKLibraryEvalExamples.sln](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryEvalExamples.sln) project shows how to evaluate a model in C#.

## Java API (Experimental)

The CNTK Java API supports model evaluation in Java. This API is still experimental and subject to change. It is provided as a jar file (cntk.jar) that can be included in Java projects.

Please refer to [Windows](./CNTK-Library-Evaluation-on-Windows.md#using-java) and [Linux](./CNTK-Library-Evaluation-on-Linux.md#using-java) instructions for how to use the Java API.

The [Java example](https://github.com/Microsoft/CNTK/blob/master/Tests/EndToEndTests/EvalClientTests/JavaEvalTest/src/Main.java) shows how to evaluate a model in Java.

