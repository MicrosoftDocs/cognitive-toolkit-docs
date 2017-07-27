---
title:   CNTK production test configurations
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   07/31/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK production test configurations

Here are the current configurations that are used in CNTK team environment for automated building and testing. The presented set of product versions is not restrictive, i.e. CNTK may well work in many other configurations, however this is exactly how we test our builds. Also each pull request reviewed is being tried in the same configurations and with the same set of tests as our regular builds.

## Windows
### Operating System
* Windows 8.1 Pro (64 bit)
* Windows 10 (64 bit)
* Windows Server 2012 R2 Standard and later

### Compiler
* Visual Studio Ultimate 2015 Update 3

### MPI
* Microsoft MPI v. 7.0

### Math Library
* Intel® Math Kernel Library 11.3 Update 3

### NVIDIA Components
* NVIDIA CUDA 8.0
* NVIDIA cuDNN 6.0 for CUDA
* NVIDIA CUB 1.4.1

### OpenCV
* OpenCV v.3.1.0

### zlib Library
* zlib v.1.2.8

### libzip Library
* libzip v.1.1.3

### Java
* Java SE Development Kit 8 v1.8.0\_131, 64-bit

## Linux
### Operating System
* Ubuntu 14.04 LTS (64 bit)

### Compiler
* GNU C++ 4.8.4

### MPI
* Open MPI v. 1.10.3

### Math Library
* Intel® Math Kernel Library 11.3 Update 3

### NVIDIA Components
* NVIDIA CUDA 8.0
* NVIDIA cuDNN 6.0 for CUDA 8
* NVIDIA CUB 1.4.1

### OpenCV
* OpenCV v.3.0.0

### zlib Library
* zlib v.1.2.8

### libzip Library
* libzip v.1.1.2

### Java
* OpenJDK 7, 64-bit
