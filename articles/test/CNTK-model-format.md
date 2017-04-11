
CNTK allows users to save a model into a file for future use. It can be done by 
* specifying "modelPath" in the config file when using BrainScript/cntk.exe, or
* [save_model()](https://www.cntk.ai/pythondocs/cntk.ops.html#cntk.ops.functions.Function.save_model) in python, or
* [SaveModel()](https://github.com/Microsoft/CNTK/blob/master/Source/CNTKv2LibraryDll/API/CNTKLibrary.h) in C++ when using [CNTK Library API](./CNTK-Library-API).
 
There are two different file formats to store the model in.
* **The model-v1 format**. This format was originally used prior to the CNTK2 version. A model is stored in the model-v1 format when it is saved by BrainScript/cntk.exe.

* **The model-v2 format**. With CNTK2, a Protobuf based format is introduced, which is now known as the model-v2 format. A model is saved in this format only when using [CNTK Library API](./CNTK-Library-API)   
  * by [save_model()](https://www.cntk.ai/pythondocs/cntk.ops.html#cntk.ops.functions.Function.save_model) in python, or
  * by [SaveModel()](https://github.com/Microsoft/CNTK/blob/master/Source/CNTKv2LibraryDll/API/CNTKLibrary.h) in C++.

The following table gives an overview about which model format is created and consumed by which CNTK binary.
 
|                       |Model Creation  | Model Evaluation | Lanugage Support |
|:----------------------|:---------------|:-----------------|:-----------------|
| model-v1 format | cntk.exe| [EvalDll](./EvalDll-Evaluation-Overview), [CNTK Library](./CNTK-Library-Evaluation-Overview) | BrainScript, C++, C#/.NET
| model-v2 format | CNTK Library | [CNTK Library](./CNTK-Library-Evaluation-Overview)  | C++, C#/.NET, Python
