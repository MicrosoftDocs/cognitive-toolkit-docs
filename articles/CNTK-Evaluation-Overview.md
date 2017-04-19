Once you have trained a model, you need the functionality to evaluate the model in your target environment. As a reminder, 
there are several ways to create models with CNTK, and there are two different formats to store the model in.

The original CNTK (prior to the CNTK 2.0 version) only supports a format we call now the model-V1 format. It was originally created by using CNTK.EXE with BrainScript. With CNTK 2.0 a Protobuf based format was introduced, which is now 
known as the model-V2 format. The following table presents an overview on creating the different model formats:

|  _model-creation_     | model-v1 | model-v2 |
|:----------------------|:---------|:---------|
| CNTK.EXE (BrainScript) |     YES     | NO |
| CNTK-library (Python, C++) | deprecated | YES |

For more details on creating the different model formats refer to the [CNTK model format](./CNTK-model-format) page.

## CNTK model evaluation methods

Aside from training a model, Microsoft Cognitve Toolkit provides different ways of evaluating the model:

|  _model-evaluation_     | features | model-v1 | model-v2 |
|:----------------------|:----|:----|:-----|
| [CNTK.EXE](./CNTK-Evaluation-using-cntk.exe) | BrainScript |    YES     | NO |
| | GPU and CPU support |||
| [CNTK-EvalDLL](./EvalDll-Evaluation-Overview) |  C++, C#/.NET | YES | NO |
| | ASP and Azure support |||
| | Nuget Package for CPU-Only support |||
|[CNTK-library](./CNTK-Library-Evaluation-Overview) | C++, C#/.NET and Python | YES | YES | 
| | ASP and Azure support |||
| | GPU and CPU |||
| | Nuget Packages |||
| | Support multiple parallel requests |||
| | Optimized memory usage by parameter sharing ||| 

The following pages provide detailed information about model evaluation in different scenarios:

- [CNTK-library evaluation on Windows](./CNTK-Library-Evaluation-on-Windows)
- [CNTK-library evaluation on Linux](./CNTK-Library-Evaluation-on-Linux)
- [CNTK-library evaluation with Python](./How-do-I-Evaluate-models-in-Python)

***

- [Evaluation using NuGet-Packages](./NuGet-Package)
- [Evaluation in Azure](./Evaluate-a-model-in-an-Azure-WebApi)

***

- [EvalDll evaluation on Windows](./EvalDll-Evaluation-on-Windows)
- [EvalDll evaluation on Linux](./EvalDll-Evaluation-on-Linux)    
- [Evaluating a model using CNTK.EXE](./CNTK-Evaluation-using-cntk.exe)    
