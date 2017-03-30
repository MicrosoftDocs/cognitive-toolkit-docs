## Overview
The [CNTK Library API](./CNTK-Library-API) allows to evaluate both CNTK model-v1 and model-V2 [format](./CNTK-model-format). The CNTK Library can be consumed on Windows and Linux by C++, Python, C# and other .NET languages. 

New features of the CNTK Library for Evaluation include
* Support both CPU and GPU device.
* [Support multiple evaluation requests in parallel](./CNTK-Eval-Examples#examples-for-evaluating-multiple-requests-in-parallel).
* Optimize memory usage by parameter sharing of the same model between multiple threads. This will significantly reduce memory usage when running evaluation in a service environment. 

The following pages provide detailed information about model evaluation using CNTK Library.
* [CNTK-library evaluation on Windows](./CNTK-Library-Evaluation-on-Windows)
* [CNTK-library evaluation on Linux](./CNTK-Library-Evaluation-on-Linux)
* [CNTK-library evaluation with Python](./Evaluate-a-saved-convolutional-network)
* [Evaluation using NuGet-Packages](./NuGet-Package)

