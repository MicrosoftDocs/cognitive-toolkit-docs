---
title:   CNTK_2_0_Beta_1_Release_Notes
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   11/16/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.prod: cntk
ms.devlang:   NA
---

# CNTK_2_0_Beta_1_Release_Notes

With this Release we start delivering CNTK V2 - a major upgrade of Microsoft Cognitive Toolkit (former Microsoft Computational Network Toolkit).

This is a summary of new features delivered with the first Beta release of CNTK V.2.0.

## CNTK as a Library. Python and C++ API

From now on you can use CNTK not only by running CNTK executable and programming the Networks with BrainScript, but also as a software library.

The following interfaces are supported:

* Python
* C++

Read more about CNTK Library API [here](../CNTK-Library-API.md).  
Detailed description of the Python API can be found in [CNTK Python API Documentation](https://cntk.ai/pythondocs/).

## Python Examples and Tutorials (Jupyter Notebooks)

We have prepared a set of Python Examples and Tutorials (the latter implemented as (Jupyter Notebooks). You will find all information at these locations:

* [Python Examples](https://cntk.ai/pythondocs/examples.html)
* [Python Tutorials (Jupyter Notebooks)](https://cntk.ai/pythondocs/tutorials.html)

## Protocol Buffers serialization

CNTK now supports Google Protocol Buffers serialization for Model and Checkpoint saving.

## New automated installation procedures

With this release we introduce the possibility of automated environment configuration and installation of all CNTK pre-requisites from the script driven binary install option. 

See more on the new installation options [here](../Setup-CNTK-on-your-machine.md).

## Fast R-CNN algorithm

CNTK now supports [object recognition using Fast R-CNN](../Object-Detection-using-Fast-R-CNN.md) algorithm.


## CNTK Evaluation library

**Please note that in this release there will be NO update of CNTK NuGet package.** We will update the NuGet package in the forthcoming releases.

The following features are added to the CNTK Evaluation library:
* C++ Evaluation is now using CNTK API. Examples available under Examples\Evaluation\CPPEvalV2Client.
* Support of multiple threads running evaluation requests in parallel. 
* Support of sharing model parameters with CNTK API. 
* Support of evaluation on GPU device with CNTK API. 
