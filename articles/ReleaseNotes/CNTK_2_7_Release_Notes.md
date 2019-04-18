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

Dear community, 
With our ongoing contributions to ONNX and the ONNX Runtime, we have made it easier to interoperate within the AI framework ecosystem and to access high performance, cross-platform inferencing capabilities for both traditional ML models and deep neural networks. Over the last few years we have been privileged to develop such key open-source machine learning projects, including the Microsoft Cognitive Toolkit, which has enabled its users to leverage industry-wide advancements in deep learning at scale. 
Today’s 2.7 release will be the last main release of CNTK. We may have some subsequent minor releases for bug fixes, but these will be evaluated on a case-by-case basis. There are no plans for new feature development post this release. 
The CNTK 2.7 release has full support for ONNX 1.4.1, and we encourage those seeking to operationalize their CNTK models to take advantage of ONNX and the ONNX Runtime. Moving forward, users can continue to leverage evolving ONNX innovations via the number of frameworks that support it. For example, users can natively export ONNX models from PyTorch or convert TensorFlow models to ONNX with the TensorFlow-ONNX converter. 
We are incredibly grateful for all the support we have received from contributors and users over the years since the initial open-source release of CNTK. CNTK has enabled both Microsoft teams and external users to execute complex and large-scale workloads in all manner of deep learning applications, such as historical breakthroughs in speech recognition achieved by Microsoft Speech researchers, the originators of the framework. 
As ONNX is increasingly employed in serving models used across Microsoft products such as Bing and Office, we are dedicated to synthesizing innovations from research with the rigorous demands of production to progress the ecosystem forward. 
Above all, our goal is to make innovations in deep learning across the software and hardware stacks as open and accessible as possible. We will be working hard to bring both the existing strengths of CNTK and new state-of-the-art research into other open-source projects to truly broaden the reach of such technologies. 
With gratitude, 
-- The CNTK Team 

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


