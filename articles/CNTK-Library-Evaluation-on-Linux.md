"---
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

# CNTK Library Evaluation on Linux"

The CNTK Library on Linux is provided as C++ and Python library.

## Using C++
The usage pattern on Linux is the same as that on Windows.  
   
The evaluation library, `libCntk.Core`, can be found under `cntk/lib` in the CNTK binary package. If you build CNTK from source code, the `libCntk.Core` is available in the `lib` folder of the build directory. 

Any program using the evaluation library needs to link the libraries `libCntk.Core` and `libCntk.Math`, e.g.
```
-lCntk.Core-<VERSION> -lCntk.Math-<VERSION>
```
, and set the appropriate search path for these libraries. Please use the same build flavor (Debug/Release) and [the same compiler version](./Setup-CNTK-on-Linux.md#c-compiler) as the one used to create the libraries. The [Examples/Evaluation/CNTKLibraryCPPEvalCPUOnlyExamples](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryCPPEvalCPUOnlyExamples) and [Examples/Evaluation/CNTKLibraryCPPEvalGPUExamples](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryCPPEvalGPUExamples) in the CNTK source code illustrates the usage pattern in Linux. The [Makefile](https://github.com/Microsoft/CNTK/blob/master/Makefile) contains the target CNTKLIBRARY_CPP_EVAL_EXAMPLES showing how to build the example.

For details on the CNTK Library for evaluation, please refer to the [CNTK Library C++ Evaluation Interface](./CNTK-Library-Native-Eval-Interface.md) page in this wiki.

### Using Python
You can use Python to evaluate a pre-trained model. Examples can be found [here](./How-do-I-Evaluate-models-in-Python.md).
