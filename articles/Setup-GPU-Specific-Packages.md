---
title:   Setup GPU Specific packages
author:    thiagofc
ms.author:   thiagofc
ms.date:   02/15/2018
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.service:  Cognitive-services
ms.devlang:   python
---

# Setup GPU specific packages on Windows 

This section outlines the packages you need to setup in order for CNTK to leverage NVIDIA GPUs.

## Checking your GPU compatibility

You need a [CUDA](https://developer.nvidia.com/cuda-zone)-compatible graphic card to use CNTK GPU capabilities. You can check whether your card is CUDA-compatible [here](https://developer.nvidia.com/cuda-gpus) and [here (for older cards)](https://developer.nvidia.com/cuda-legacy-gpus). Your GPU card [Compute Capability (CC)](http://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#compute-capability) must be 3.0 or more.

In the following steps we will install the NVidia development tools required to build the Microsoft Cognitive Toolkit as well as NVidia support libraries. As the last step (after you installed all aforementioned NVidia tools!), you should check that you have the latest graphic card driver installed.

Make sure the directory `C:\Program Files\NVIDIA Corporation\NVSMI` exists in your system.

* Quick installation check: If you followed the instruction above and used the same paths, the command `dir C:\Program Files\NVIDIA Corporation\NVSMI\nvml.dll` will succeed.

## Latest GPU card driver

Install the latest driver for your GPU card:
* Select your card and download the driver pack from [this download location](http://www.nvidia.com/Download/index.aspx?lang=en-us)
* Run the driver installation procedure

# NVIDIA CUDA 9.0

Download and install the NVIDIA CUDA 9.0 Toolkit:

* Download the required package from [this download page](https://developer.nvidia.com/cuda-90-download-archive)
* Run the installation procedure

Make sure that the following CUDA environment variables are set to the correct path (the NVIDIA Cuda installer will create these for you). Default installation paths are assumed:

```
CUDA_PATH="C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.0"
CUDA_PATH_V9_0="C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.0"
```

* Quick installation check: If you followed the instruction above and used the same paths, the command `dir C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.0\bin\cudart64_90.dll` will succeed.

# cuDNN

Install `NVIDIA CUDA Deep Neural Network library` also known as [cuDNN](https://developer.NVIDIA.com/cuDNN) in the version **NVIDIA: cuDNN v7.0 for CUDA 9.0** from this [link](http://developer.download.NVIDIA.com/compute/redist/cudnn/v7.0.4/cudnn-9.0-windows10-x64-v7.zip). This version is suitable for Windows 8.1, Windows 10, as well as Windows Server 2012 R2 and later.

* Extract the archive to a folder on your local disk, e.g. to `C:\local\cudnn-9.0-v7.0\`

* Quick installation check: If you followed the instruction above and used the same paths, the command `dir C:\local\cudnn-9.0-v7.0\cuda\bin\cudnn64_7.dll` will succeed.

# CUB

> [!IMPORTANT]
> If you are installing CNTK for Python, you may skip this step.

> [!IMPORTANT]
> Install NVIDIA CUB using the exact version specified below. This is necessary because it is expected by the CNTK build configuration program.

* Download NVIDIA CUB v.1.7.4 [from this download link](https://github.com/NVlabs/cub/archive/1.7.4.zip)
* Extract the archive to a folder on your local disk (we assume `c:\local\cub-1.7.4`).

* Quick installation check. If you followed the instruction above and used the same paths, this command `dir C:\local\cub-1.7.4\cub\cub.cuh` will succeed.
