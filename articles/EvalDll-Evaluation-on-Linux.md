---
title:   EvalDLL evaluation on Linux
author:    chrisbasoglu
date:    04/14/2017
ms.author:   cbasoglu
ms.date:   04/14/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   csharp, cpp
---

# EvalDLL evaluation on Linux

The `EvalDll` library on Linux is provided as a C++ library. 

The usage pattern for evaluation is the following:

1. Get an instance of the evaluation engine either using GetEvalF() (for the `float` data type) or GetEvalD() (for the `double` data type).
2. Load the model (or create the network) in the evaluation engine.
3. Evaluate some input against the model and obtain the corresponding output.
4. Dispose the model when done.

The evaluation library, `Cntk.Eval`, can be found under `cntk/lib` in the CNTK binary package. If you build CNTK from source code, The shared library `Cntk.Eval` is available in the `lib` folder of the build directory.

Any program using the evaluation library needs to link the libraries `Cntk.Core` and `Cntk.Math`, e.g.
```
-lCntk.Eval-<VERSION> -lCntk.Math-<VERSION>
```
, and set the appropriate search path for these libraries. Please use the same build flavor (Debug/Release) and [the same compiler version](./Setup-CNTK-on-Linux.md#c-compiler) as the one used to create the libraries. The [Examples/Evaluation/CPPEvalClient](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CPPEvalClient) in the CNTK source code illustrates the usage pattern in Linux. The [Makefile](https://github.com/Microsoft/CNTK/blob/master/Makefile) contains the target EVAL_SAMPLE_CLIENT showing how to build the example.

For details on the C++ API provided by EvalDll, please refer to the [EvalDll C++ API](./EvalDll-Native-API.md) page.
