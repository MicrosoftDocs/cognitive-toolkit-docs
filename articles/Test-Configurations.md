---
title:   CNTK Production Test Configurations
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   08/07/2017
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   NA
---
# CNTK Production Test Configurations

This page lists configurations that are used for automated test of the Microsoft Cognitive Toolkit. It doesn't distinguish explicitly between build and test environment, for a detailed overview of the individual requirements you should consult the [development environment setup page](./Setup-development-environment.md) for a build environment, and the [setup from precompiled binaries page](./Setup-CNTK-on-your-machine.md) for the test environment.

The presented set of product versions is not restrictive, i.e. CNTK may well work in many other configurations.

## Windows

### Operating System

* Windows 8.1 Pro (64 bit)
* Windows 10 (64 bit)
* Windows Server 2012 R2 Standard and later

### Compiler

* Visual Studio Enterprise 2017

### MPI

* Microsoft MPI v. 7.0

### Math Library

* Intel® MKLML library

### NVIDIA Components

* NVIDIA CUDA 9.0
* NVIDIA cuDNN 7.0 for CUDA 9.0
* NVIDIA CUB 1.7.4

### OpenCV

* OpenCV v.3.1.0

### zlib Library

* zlib v.1.2.8

### libzip Library

* libzip v.1.1.3

### Java

* Java SE Development Kit 8 v1.8.0\_131, 64-bit

### Anaconda Python for Windows

* Anaconda3 4.1.1 (64 bit)

## Linux

### Operating System

* Ubuntu 16.04 LTS (64 bit)

### Compiler

* GNU C++ 5.4.0

### MPI

* Open MPI v. 1.10.7

### Math Library

* Intel® MKLML library

### NVIDIA Components

* NVIDIA CUDA 9.0
* NVIDIA cuDNN 7.0 for CUDA 9.0
* NVIDIA CUB 1.7.4

### OpenCV

* OpenCV v.3.1.0

### zlib Library

* zlib v.1.2.8

### libzip Library

* libzip v.1.1.2

### Java

* OpenJDK 7, 64-bit

### Anaconda Python for Linux

* Anaconda3 4.1.1 (64 bit)
