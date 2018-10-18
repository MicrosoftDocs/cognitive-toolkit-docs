---
title:   Setup Linux Binary Manual
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:  07/31/2017
ms.custom:   cognitive-toolkit
ms.topic:   quickstart
ms.devlang:   NA
---
# Setup Linux Binary Manual

## Linux binary manual installation

This page will walk you through the process of installing the Microsoft Cognitive Toolkit (CNTK)
based on a binary distribution we have prepared and you can download from our website.

Note: You can find an overview about all the available installation options for CNTK on [this page](./Setup-CNTK-on-your-machine.md).

We will install the CNTK binaries, the CNTK prerequisites, and create (or update) a Python 2.7, 3.5, or 3.6 environment on your computer. The changes are as much localized as possible to not impact any other installed software.

> [!NOTE]
> If you previously installed an earlier version of the CNTK Python package, you can skip steps 1 through 3 below and directly jump to step 4 to update your existing CNTK package installation from your Python 2.7, 3.5, or 3.6 environment.

**Step 1**: Download and install prerequisites

**Docker** users please follow the instructions [here](./CNTK-Docker-Containers.md). 
Others please continue reading.

CNTK V2 on Linux requires the following prerequisites to be installed from the links below: 

* [C++ Compiler](./Setup-CNTK-on-Linux.md#c-compiler)
* [Open MPI](./Setup-CNTK-on-Linux.md#open-mpi)

> [!IMPORTANT]
> We strongly recommend to follow the Open MPI installation procedure described by the link above.

* For GPU systems ensure that you have the [latest NVIDIA driver](http://www.nvidia.com/drivers)
* Download the required binary package from [CNTK Releases page](https://github.com/Microsoft/CNTK/releases) and extract it to your machine.

**Step 2**: CNTK setup for Python

* Please follow [these instructions](./Setup-Linux-Python.md) to set up CNTK for Python on your machine.
