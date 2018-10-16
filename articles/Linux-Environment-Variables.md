---
title:   Linux Environment Variables
author:    wolfma61
ms.author:   wolfma
ms.date:   07/12/2017
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:  conceptual
ms.devlang:   NA
---
# Linux Environment Variables

If you are building the Microsoft Cognitive Toolkit on your own machine, `configure` and `Makefile` scripts support a limited set of installation paths for all dependent components. In addition the build process and the Microsoft Cognitive Toolkit use environment variables to locate components. Add the following environment variable as required to your current session and your .bashrc profile (prepending the new path, to ensure that the component required by CNTK is used as opposed to a default version available through the OS).

This page lists the environment variables which are used by the CNTK build process and CNTK itself to find required components. It also lists the *preferred* location for these components. The preferred location is mirroring the configuration of our internal automated build and test machines. The preferred location is also the location used in the documentation to describe the installation process.

|Environment Variable | Preferred Location |
|:--------|:------------|
| **[OpenMPI](./Setup-CNTK-on-Linux.md#open-mpi) (required)]** |
| PATH | /usr/local/mpi/bin
| LD_LIBRARY_PATH | /usr/local/mpi/lib
| |
| **[LIBZIP](./Setup-CNTK-on-Linux.md#libzip) (required)** |
| LD_LIBRARY_PATH | /usr/local/lib
| |
| **[CUDA 9](./Setup-CNTK-on-Linux.md) (required)** |
| PATH | /usr/local/cuda-9.0/bin
| LD_LIBRARY_PATH | /usr/local/cuda-9.0/lib64
| |
| **[cuDNN](./Setup-CNTK-on-Linux.md)** |
| LD_LIBRARY_PATH | /usr/local/cudnn-7.0/cuda/lib64 |




# Additional Environment Variables

There are additional environment variables which can influence the compilation process:

|Environment Variable | |
|:------------|:-------------|
|CNTK_CUDA_CODEGEN_DEBUG CNTK_CUDA_CODEGEN_RELEASE | With these environment variables you define the NVidia Compiler target architectures. For example, setting a variable to `-gencode arch=compute_52,code=\"sm_52,compute_52\"` will only build level 5.2 compatible cubin and PTX information. For detailed information about this refer to the NVidia Compiler documentation.

**More information**

* [Setup CNTK on your machine](./Setup-CNTK-on-your-machine.md)
