---
title:   CNTK Archive Page
author:    wolfma61
ms.author:   wolfma
ms.date:   06/22/2017
ms.custom:   cognitive-toolkit
ms.topic:   archive
ms.service:  cognitive-services
ms.devlang:   NA
---

# CNTK Archive Page

This page lists documentation pages which have been moved out of the main documentation flow. They might contain additional information that is already presented in a different location, or might contain information that is only relevant to interim releases (beta versions) of the product.

* [Breaking Changes between CNTK2-beta15 and CNTK2-beta16](./Breaking-changes-in-Master-compared-to-beta15.md)
* [CNTK moves to Cuda 8](./CNTK-move-to-Cuda8.md)
* [Migrate from VisualStudio 2013 to VisualStudio 2015](./Setup-Migrate-VS13-to-VS15.md)
* [News 2016](./News-2016.md)
* [Shared library naming convention](./CNTK-Shared-Libraries-Naming-Format.md)

## Model Evaluation using EvalDll

Prior to the CNTK 2.0 version, the CNTK EvalDLL was used to evaluate model trained by using cntk.exe with BrainScript. The EvalDLL 
is still supported, but works only for the model created by cntk.exe with BrainScript. It can not be used to evaluate models created 
by CNTK 2.0 using Python. We strongly recommend to use the CNTK 2.0 Libraries for evaluation, as it supports model formats and provides more features. 

More information about using EvalDll can be found in the following pages.

* [Model Evaluation using cntk.exe](./CNTK-Evaluation-using-cntk.exe)
* [EvalDLL Evaluation Overview](.//EvalDLL-Evaluation-Overview)
* [EvalDLL evaluation on Windows](./EvalDLL-Evaluation-on-Windows)
* [EvalDLL evaluation on Linux](./EvalDLL-Evaluation-on-Linux)
* [EvalDLL evaluation in Azure](./Evaluate-a-model-in-an-Azure-WebApi)
* [EvalDLL Managed API](./EvalDll-Managed-API)
* [EvalDLL Native API](./EvalDll-Native-API)
* [EvalDLL Evaluate Hidden Layers](./CNTK-Evaluate-Hidden-Layers)
* [EvalDLL Evaluate Image Transforms](.//CNTK-Evaluate-Image-Transforms)
* [EvalDLL Evaluate Multiple Models](./CNTK-Evaluate-Multiple-Models)

