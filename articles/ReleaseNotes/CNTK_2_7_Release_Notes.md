---
title:   CNTK_2_7_Release_Notes
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   04/01/2019
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.prod: cntk
ms.devlang:   NA
---

# CNTK v2.7 Release Notes

## Highlights of this release
* Moved to CUDA 10 for both Windows and Linux.
* Support advance RNN loop in ONNX export.
* Export larger than 2GB models in ONNX format.
* Support FP16 in Brain Script train action.

## CNTK support for CUDA 10

###CNTK now supports CUDA 10. This requires an update to build environment to Visual Studio 2017 v15.9 for Windows.

To setup build and runtime environment on Windows:
* Install [Visual Studio 2017](https://www.visualstudio.com/downloads/). Note: going forward for CUDA 10 and beyond, it is no longer required to install and run with the specific VC Tools version 14.11.
* Install [Nvidia CUDA 10](https://developer.nvidia.com/cuda-downloads?target_os=Windows&target_arch=x86_64)
* From PowerShell, run:
    [DevInstall.ps1](../Tools/devInstall/Windows/DevInstall.ps1)
* Start Visual Studio 2017 and open [CNTK.sln](./CNTK.sln).

To setup build and runtime environment on Linux using docker, please build Unbuntu 16.04 docker image using Dockerfiles [here](./Tools/docker). For other Linux systems, please refer to the Dockerfiles to setup dependent libraries for CNTK.

##Support advance RNN loop in ONNX export
CNTK models with recursive loops can be exported to ONNX models with scan ops.

##Export larger than 2GB models in ONNX format
To export models larger than 2GB in ONNX format, use cntk.Function API:
save(self, filename, format=ModelFormat.CNTKv2, use_external_files_to_store_parameters=False)
with 'format' set to ModelFormat.ONNX and use_external_files_to_store_parameters set to True.
In this case, model parameters are saved in external files. Exported models shall be used with external parameter files when doing model evaluation with onnxruntime.

***2018-11-26.***  
[Netron](https://github.com/lutzroeder/netron) now supports visualizing CNTK v1 and CNTK v2 `.model` files.

<img src=https://cntk.ai/Images/netron/netron-cntk-dark-1.png alt="NetronCNTKDark1" width="300"> <img src=https://cntk.ai/Images/netron/netron-cntk-light-1.png alt="NetronCNTKLight1" width="300">


