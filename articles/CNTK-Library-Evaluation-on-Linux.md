---
title:   CNTK Library Evaluation on Linux
author:    chrisbasoglu
date:    04/09/2017
ms.author:   cbasoglu
ms.date:   04/09/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   cpp
---

# CNTK Library Evaluation on Linux

The CNTK Library on Linux is available in C++, Python and Java.

## Using C++
The usage pattern on Linux is the same as that on Windows.  
   
The evaluation library, `libCntk.Core-<Version>.so`, can be found under `cntk/lib` in the CNTK binary package. If you build CNTK from source code, the `libCntk.Core-<Version>.so` is available in the `lib` folder of the build directory.

Any program using the evaluation library needs to link the libraries `libCntk.Core` and `libCntk.Math`, and set the appropriate search path for these libraries. 
```
-lCntk.Core-<VERSION> -lCntk.Math-<VERSION>
```
Please use the same build flavor (Debug/Release) and [the same compiler version](./Setup-CNTK-on-Linux.md#c-compiler) as the one used to create the libraries. The [Examples/Evaluation/CNTKLibraryCPPEvalCPUOnlyExamples](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryCPPEvalCPUOnlyExamples) and [Examples/Evaluation/CNTKLibraryCPPEvalGPUExamples](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryCPPEvalGPUExamples) in the CNTK source code illustrates the usage pattern in Linux. The [Makefile](https://github.com/Microsoft/CNTK/blob/master/Makefile) contains the target CNTKLIBRARY_CPP_EVAL_EXAMPLES showing how to build the example.

Please refer to the [CNTK Library C++ Evaluation Interface](./CNTK-Library-Native-Eval-Interface.md) page for APIs in the CNTK C++ Library.

## Using Python
You can use Python to evaluate a pre-trained model. Examples can be found [here](./How-do-I-Evaluate-models-in-Python.md).

## Using Java
CNTK also provides APIs for evaluating model in Java application. Please note that the CNTK Java API is still experimental and subject to change.

The [Java example](https://github.com/Microsoft/CNTK/blob/master/Tests/EndToEndTests/EvalClientTests/JavaEvalTest/src/Main.java) shows how to evaluate a CNN model using the Java API.

For using CNTK Java Library, add the `cntk.jar` file to the `classpath` of your Java project. If you are working with an IDE you should add this as an unmanaged jar. The cntk.jar file can be found in the CNTK binary release package (in the folder cntk/cntk/lib/java). You can also build cntk.jar from CNTK source. Please also set `java.library.path` to the directory containing `libCntk.Core.JavaBinding-<Version>.so`. If you use CNTK binary release package, please make sure that the pre-requisites have been installed as described in the [Linux binary manual installation](./Setup-Linux-Binary-Manual.md) page, and set the LD_LIBRARY_PATH as follows (assuming the CNTK binaries are installed to /home/username/cntkbin)
```
    export LD_LIBRARY_PATH=/home/username/cntkbin/cntk/lib:/home/username/cntkbin/cntk/dependencies/lib:$LD_LIBRARY_PATH
```
If you get `UnsatisfiedLinkErrors` in Java, it is typically because that the directory is not in the LD_LIBRARY_PATH (or in the wrong order).

The Java library is currently built and tested with 64-bit OpenJDK 7.
