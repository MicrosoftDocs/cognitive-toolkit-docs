---
title:   CNTK model format
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   06/01/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript,cpp, csharp,dotnet,python
---

# CNTK model format

CNTK allows users to save a model into a file for future use. It can be done by 
* specifying "modelPath" in the config file when using BrainScript/cntk.exe, or
* [save()](https://cntk.ai/pythondocs/cntk.ops.functions.html#cntk.ops.functions.Function.save) in Python, or
* [Save()](https://github.com/Microsoft/CNTK/blob/master/Source/CNTKv2LibraryDll/API/CNTKLibrary.h) in C++ when using [CNTK Library API](./CNTK-Library-API.md).
 
There are two different file formats to store the model in.
* **The model-v1 format**. This format was originally used prior to the CNTK2 version. A model is stored in the model-v1 format when it is saved by BrainScript/cntk.exe.

* **The model-v2 format**. With CNTK2, a Protobuf based format is introduced, which is now known as the model-v2 format. A model is saved in this format only when using [CNTK Library API](./CNTK-Library-API.md)   
  * by [save()](https://cntk.ai/pythondocs/cntk.ops.functions.html#cntk.ops.functions.Function.save) in Python, or
  * by [Save()](https://github.com/Microsoft/CNTK/blob/master/Source/CNTKv2LibraryDll/API/CNTKLibrary.h) in C++.

The following table gives an overview about which model format is created and consumed by which CNTK binary.
 
|                       |Model Creation  | Model Evaluation | Lanugage Support |
|:----------------------|:---------------|:-----------------|:-----------------|
| model-v1 format | cntk.exe| [cntk.exe](./CNTK-Evaluation-using-cntk.exe.md) [EvalDll](./EvalDll-Evaluation-Overview.md), [CNTK Library](./CNTK-Library-Evaluation-Overview.md) | BrainScript, C++, C#/.NET
| model-v2 format | CNTK Library | [CNTK Library](./CNTK-Library-Evaluation-Overview.md)  | C++, C#/.NET, Java, Python

