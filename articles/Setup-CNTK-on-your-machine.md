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

The Microsoft Cognitive Toolkit (CNTK) supports both 64-bit Windows and 64-bit Linux platforms. Upon completing the installation, you can return to the [Getting Started](https://cntk.ai/pythondocs/gettingstarted.html) section.

It is recommended you install CNTK from precompiled binaries, but you can also install and build the CNTK codebase on your machine. More details about this option can be found in the "CNTK Source Code & Development" section.

## Install CNTK from Precompiled Binaries

To install the latest precompiled binaries to your machine, follow the instructions here:

|Windows                  | Linux                   |
|:------------------------|:------------------------|
|[Python-only installation](./Setup-Windows-Python.md)<br>Simple pip install of CNTK lib for use in Python| [Python-only installation](./Setup-Linux-Python.md)<br>Simple pip install of CNTK lib for use in Python |
|[Script-driven installation](./Setup-Windows-Binary-Script.md)<br>Script that installs CNTK Python lib and CNTK.exe for BrainScript | [Script-driven installation](./Setup-Linux-Binary-Script.md)<br>Script that installs CNTK Python lib and CNTK.exe for BrainScript 
|[Manual installation](./Setup-Windows-Binary-Manual.md)<br>Manually install CNTK Python lib, CNTK.exe for BrainScript, and dependencies  | [Manual installation](./Setup-Linux-Binary-Manual.md)<br>Manually install CNTK Python lib,CNTK.exe for BrainScript, and dependencies
|                                                     | [Docker installation](./CNTK-Docker-Containers.md)

## Installation as Azure Virtual Machine or Linux Docker container

You may use CNTK via Microsoft Azure Virtual Machine offering (Windows and Linux) or install it as a Docker container (Linux). See the corresponding sections:

* [CNTK on Azure](./CNTK-on-Azure.md)
* [CNTK Docker Containers](./CNTK-Docker-Containers.md)

## Usage and Samples

If you want to learn more about CNTK usage and how to execute the provided samples, you find more information on the following pages

* [CNTK usage overview](./CNTK-usage-overview.md)
* [Examples](./Examples.md)
