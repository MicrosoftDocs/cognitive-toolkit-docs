---
title:   CNTK_2_0_Beta_5_Release_Notes
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   11/25/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK_2_0_Beta_5_Release_Notes

This is a summary of new features delivered with the Beta 5 release of CNTK V.2.0.

## Windows: The Microsoft Cognitive Toolkit and NVIDIA Cuda 8

With this Beta release the Windows version of the Cognitive Toolkit supports NVIDIA Cuda 8. The binary beta 5 packages are built using the NVidia Cuda 8 toolkit. If you are a developer and building CNTK on your own system you can still continue using Cuda 7.5. This will change soon, please read details [here](../CNTK-move-to-Cuda8.md).

## Linux: The Microsoft Cognitive Toolkit and NVIDIA Cuda 8

In this Beta the Microsoft Cognitive Toolkit is only supporting NVIDIA Cuda 7.5 on Linux. We expect to move the Linux version of the toolkit to Cuda 8 shortly.

## CNTK Evaluation library. NuGet package 

A new NuGet package with the latest eval DLL (managed and native) is [available](../NuGet-Package.md). The `EvaluateRgbImage` function in the [managed Eval API](../EvalDll-Managed-API.md) improves speed of image evaluation. 

 **IMPORTANT!** In Visual Studio *Manage NuGet Packages* Window change the default option *Stable Only* to *Include Prerelease*. Otherwise the package will not be visible. The Package version should be ```2.0-beta5```.

## Performance

In the CNTK Text Format deserializer the memory footprint is reduced significantly. This will improve performance and scalability. 

## Stability Improvements

We continue fine tuning features and fixing bugs - thank you once again for the constant feedback!

## Documentation and Tutorials 

We improve/update documentation continuously. We also are adding new tutorials as quickly as possible. If you have a tutorial you want to contribute, please let us know!

The latest tutorial we added covers [Sequence-to-Sequence](https://github.com/Microsoft/CNTK/blob/v2.0.beta5.0/Tutorials/CNTK_204_Sequence_To_Sequence.ipynb).
