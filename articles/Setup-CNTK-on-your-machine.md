---
title:   Setup CNTK on your machine
author:    chrisbasoglu
date:    04/20/2017
ms.author:   cbasoglu
ms.date:   04/20/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.service:  Cognitive-services
ms.devlang:   python, brainscript, 
---

# Setup CNTK on your machine

## Simple CNTK Installation

Microsoft Cognitive Toolkit (CNTK) is supported for both Windows and Linux (Ubuntu 16.04). You can do a simple pip install using the commands in this section that assumes Anaconda 3 Python 3.5 environment. Please go to the next section for more installation options. 

Once installed you can return to the [Getting Started](https://docs.microsoft.com/en-us/python/cognitive-toolkit/gettingstarted) section.

> For either **Windows** or **Ubuntu 16.04**, we assume you have configured **Anaconda Python 3.5** as your active Python environment. If you meet this requirement simply run the following commands from your Python root environment to install CNTK **CPU-only** version.

On **Windows**: 
```
pip install https://cntk.ai/PythonWheel/CPU-Only/cntk-2.0rc3-cp35-cp35m-win_amd64.whl
```
On **Ubuntu 16.04**:
```
sudo apt-get install openmpi-bin
pip install https://cntk.ai/PythonWheel/CPU-Only/cntk-2.0rc3-cp35-cp35m-linux_x86_64.whl
```
You can easily fine tune your PIP installation, further change your environment configuration, tutorial installation, examples, code samples and Jupyter notebooks.
For Windows, check out [this page](./Setup-Windows-Python.md), for Linux [this one](./Setup-Linux-Python.md).

For the complete lists of CNTK installation options, see the table below.

### Install CNTK for the first time or update to a new version
-------------------------------
The Microsoft Cognitive Toolkit (CNTK) supports both 64-bit Windows and 64-bit Linux platforms. Upon completing the installation, you can return to the [Getting Started](https://docs.microsoft.com/en-us/python/cognitive-toolkit/gettingstarted) section.

You can install the complete source code of CNTK and build the binaries on your machine, but we 
also provide regular binary drops of the CNTK executables, including sample data and sample models.

#### Binary Installation (or Update) of CNTK

If you just want to download and install the latest precompiled binaries to your machine, follow the instructions here:

|Windows                  | Linux                   |
|:------------------------|:------------------------|
|[Python-only installation](./Setup-Windows-Python.md)<br>Simple pip install of CNTK lib for use in Python| [Python-only installation](./Setup-Linux-Python.md)<br>Simple pip install of CNTK lib for use in Python |
|[Script-driven installation](./Setup-Windows-Binary-Script.md)<br>Script that installs CNTK Python lib and CNTK.exe for BrainScript | [Script-driven installation](./Setup-Linux-Binary-Script.md)<br>Script that installs CNTK Python lib and CNTK.exe for BrainScript 
|[Manual installation](./Setup-Windows-Binary-Manual.md)<br>Manually install CNTK Python lib, CNTK.exe for BrainScript, and dependencies  | [Manual installation](./Setup-Linux-Binary-Manual.md)<br>Manually installCNTK Python lib,CNTK.exe for BrainScript, and dependencies
|                                                     | [Docker installation](./CNTK-Docker-Containers.md)
#### Installation and building of the CNTK codebase

If you want to take a look at the CNTK source code, compile CNTK yourself, make changes to the CNTK codebase, and contribute these changes back to the community, these are the pages for you:

|Windows                  | Linux                   |
|:------------------------|:------------------------|
|[Script-driven development setup](./Setup-CNTK-with-script-on-Windows.md) |
|[Manual development Setup](./Setup-CNTK-on-Windows.md) | [Manual Development Environment Setup](./Setup-CNTK-on-Linux.md) 
|[Migration from VS13 to VS15](./Setup-Migrate-VS13-to-VS15.md) | 
 

* [Enabling 1bit SGD](./Enabling-1bit-SGD.md)
* [Developing and Testing](./Developing-and-Testing.md)
* [CNTK Production Test Configurations](./Test-Configurations.md)

#### Installation as Azure Virtual Machine or Linux Docker container

You may use CNTK via Microsoft Azure Virtual Machine offering (Windows and Linux) or install it as a Docker container (Linux). See the corresponding sections:

* [CNTK on Azure](./CNTK-on-Azure.md)
* [CNTK Docker Containers](./CNTK-Docker-Containers.md)

#### Usage and Samples

If you want to learn more about CNTK usage and how to execute the provided samples, you find more information on the following pages

* [CNTK usage overview](./CNTK-usage-overview.md)
* [Examples](./Examples.md)
