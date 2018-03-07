---
title:   Setup CNTK on your machine
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   3/7/2018
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.service:  Cognitive-services
ms.devlang:   python, brainscript
---

# Setup CNTK on your machine

The Microsoft Cognitive Toolkit (CNTK) supports both 64-bit Windows and 64-bit Linux platforms. Upon completing the installation, you can [test your installation from Python](./Setup-Test-Python.md) or try the [tutorials](./Tutorials.md) or [examples](./Examples.md) section of the documentation.

It is recommended you install CNTK from precompiled binaries. If you want to build CNTK from source code, the required steps are described [here](./Setup-CNTK-from-source.md).

## Install CNTK from Precompiled Binaries

To install the latest precompiled binaries to your machine, follow the instructions here:

|Windows                  | Linux                   |
|:------------------------|:------------------------|
|[Python-only installation](./Setup-Windows-Python.md)<br>Simple pip install of CNTK lib for use in Python| [Python-only installation](./Setup-Linux-Python.md)<br>Simple pip install of CNTK lib for use in Python |
|[Script-driven installation](./Setup-Windows-Binary-Script.md)<br>Script that installs CNTK Python lib and CNTK.exe for BrainScript | [Script-driven installation](./Setup-Linux-Binary-Script.md)<br>Script that installs CNTK Python lib and CNTK.exe for BrainScript 
|[Manual installation](./Setup-Windows-Binary-Manual.md)<br>Manually install CNTK Python lib, CNTK.exe for BrainScript, and dependencies  | [Manual installation](./Setup-Linux-Binary-Manual.md)<br>Manually install CNTK Python lib,CNTK.exe for BrainScript, and dependencies
|                                                     | [Docker installation](./CNTK-Docker-Containers.md)

## CNTK Versions: CPU and GPU

CNTK offers two different build versions. The CPU-only build uses the optimized Intel MKLML; MKLML is released with Intel MKL-DNN as a trimmed version of Intel MKL for MKL-DNN. The GPU implementation uses highly optimized NVIDIA libraries (such as CUB and cuDNN) and supports distributed training across multiple GPUs and multiple machines. The GPU build also includes the MSR-developed 1bit-quantized SGD and block-momentum SGD parallel training algorithms, which allow for even faster distributed training in CNTK.
