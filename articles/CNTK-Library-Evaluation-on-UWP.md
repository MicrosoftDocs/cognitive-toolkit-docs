---
title:   Model Evaluation on Universal Windows Platform
author:    zhouwangzw
ms.author:   zhouwang
ms.date:  07/31/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   csharp, cpp
---

# Model Evaluation on Universal Windows Platform

[!INCLUDE[versionadded-2.1-block](includes/versionadded-2.1-block.md)]

Starting from v2.1, CNTK supports loading and evaluating models in applications running on the Universal Windows Platform (UWP). The CNTK Library for UWP is provided as a NuGet Package, `CNTK.UWP.CPUOnly`, for model evaluation on CPU devices using C++. There is also an example showing how to use CNTK UWP library in C# via C++/CX wrapper.

## Installation

The Nuget package `CNTK.UWP.CPUOnly` can be installed through the Nuget Package Manager inside Visual Studio by searching for "CNTK", or downloaded directly from nuget.org:
[https://www.nuget.org/packages/CNTK.UWP.CPUOnly](https://www.nuget.org/packages/CNTK.UWP.CPUOnly).

Please refer to the [NuGet Package](./NuGet-Package.md) page about details how to install CNTK Library NuGet Packages.

## Programming Guide

The CNTK UWP library currently provides C++ APIs for model evaluation on UWP. It implements the same APIs as the CNTK evaluation library for desktop applications. Please refer to [model evaluation for Windows overview page](./CNTK-Library-Evaluation-on-Windows.md#using-c) and [C++ Eval API](./CNTK-Library-Native-Eval-Interface.md) for details.

## Examples

The [UWPImageRecognition folder] (https://github.com/Microsoft/CNTK/tree/release/2.3/Examples/Evaluation/UWPImageRecognition) contains an example for using CNTK UWP library for model evaluation. It also shows how to use the library in C# via a C++/CX wrapper.
