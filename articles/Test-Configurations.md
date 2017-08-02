---
title:   CNTK Production Test Configurations
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   07/31/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---
# CNTK Production Test Configurations

The page lists configurations that are used for automated test of the Microsoft Cognitive Toolkit. It doesn't distinguish explicitly between build and test environment, for a detailed overview of the individual requirements you should consult the [development environment setup page](./Setup-development-environment.md) for a build environment, and the [setup from precompiled binaries page](./Setup-CNTK-on-your-machine.md) for the test environment.

The presented set of product versions is not restrictive, i.e. CNTK may well work in many other configurations.

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

* CNTK Custom MKL Library (based on Intel® Math Kernel Library 11.3 Update 3)

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

### Anaconda Python for Windows

* Anaconda3 4.1.1 (64 bit)

## Linux

### Operating System

* Ubuntu 14.04 LTS (64 bit)

### Compiler

* GNU C++ 4.8.4

### MPI

* Open MPI v. 1.10.7

### Math Library

* CNTK Custom MKL Library (based on Intel® Math Kernel Library 11.3 Update 3)

### NVIDIA Components

* NVIDIA CUDA 8.0
* NVIDIA cuDNN 6.0 for CUDA 8
* NVIDIA CUB 1.4.1

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