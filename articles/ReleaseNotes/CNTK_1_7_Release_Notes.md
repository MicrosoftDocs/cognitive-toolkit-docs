---
title:   CNTK_1_7_Release_Notes
author:    chrisbasoglu
date:    11/07/2016
ms.author:   cbasoglu
ms.date:   11/07/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK_1_7_Release_Notes

This is a summary on what's new in CNTK 1.7 Binary Release. Apart from many bug fixes we have the following new features.

## BrainScript

We have many improvements in BrainScript.

* **New library of predefined common layer types.** Using function objects, this enables very succinct definition of models by sequential composition. E.g., a simple ATIS slot tagger can be written as:
```
model = Sequential (
EmbeddingLayer {150} :
RecurrentLSTMLayer {300} :
DenseLayer {129}
)
```
Read more in the Wiki on [Layers](../BrainScript-Layers-Reference.md) and [Sequential](../Sequential.md).

* Support of **CuDNN5 RNN** which significantly improves performance.
* Support of **Common random-initialization types**, e.g.
```
W = ParameterTensor {(1024:42), init="glorotNormal"}
```
* **Dimensions inference for model parameters**. E.g.
```
W = ParameterTensor {(1024:Inferred)}
z = W * x
```
will infer the dimension marked as Inferred from the input.  
(See the complete description of ```ParameterTensor``` in the [Wiki](../Parameters-And-Constants.md#parametertensor).)

* **Curly braces in configuration and BrainScript** making it more similar to other familiar languages. See more in the [Wiki Article](../BrainScript-Basic-Concepts.md).
* We have significantly simplified Handling of **Gated Recurrent Units (GRU)** was significantly improved by adding the convenience functions to Brainscript library. Read more in the [corresponding article](https://msrstagingredirect.azurewebsites.net/en-us/cognitive-toolkit/2016/08/grus-on-cntk-with-brainscript/).

## Support of NVIDIA cuDNN 5.1

CNTK now relies on version **5.1** of NVIDIA cuDNN Library.

This in turns allows utilizing **NVIDIA GPU Cards based on *Pascal* architecture** like *GeForce GTX 1080*. However note, that NVIDIA Pascal architecture [**requires** CUDA 8.0](https://developer.nvidia.com/cuda-toolkit) while CNTK 1.7 is built with CUDA 7.5. Today [CUDA 8.0 is available as a Release Candidate version](https://developer.nvidia.com/cuda-toolkit), and if you would like to use CNTK with NVIDIA Pascal architecture cards you need to *download and install the current Release Candidate of CUDA 8.0 and build CNTK from Sources for [Windows](../Setup-CNTK-on-Windows.md) or [Linux](../Setup-CNTK-on-Linux.md) using CUDA 8.0 RC instead of CUDA 7.5*. CNTK will fully support CUDA 8.0 when it is released to production.

## Readers/Deserializers

We have the following improvements in Readers and Deserializers.

* Switch to **Mersenne Twister randomization** for the new readers (*Image, CNTKTextFormat, HTKDeserializers*) This fixes a rand bug in a distributed environment when the same samples/sequences could be picked up by several workers
* Support of IO prefetching for data
* Default input for labels (*Image* and *HTKMLF*) was switched to sparse format

The following changes were implemented for **ImageReader**: 
* Access to zip index was restructured to enable faster initialization
* Enabling Retry logic for image reading

## CNTK Model Evaluation library

V.1.7 features CNTK Evaluator both for Windows and Linux. 

For Windows you can get CNTK Eval is a NuGet Package or directly from the Binary Distribution.

Linux CNTK Evaluator is included in Linux binary distribution.

The following changes and improvements are introduced in V.1.7:
* Eval client samples (both C++ and C#) are included in the CNTK binary drop for Windows. The samples demonstrate how to use the CNTK evaluation library in C++ and C#. Instructions for using samples 
are available in the [Wiki](.).
* *EvalWrapper.dll* now has a *Strong Name*. Implementation details available in the description in the header of [*EvalWrapperAssemblyInfo.cpp*](https://github.com/Microsoft/CNTK/blob/master/Source/Extensibility/EvalWrapper/EvalWrapperAssemblyInfo.cpp).

## Unit Tests

Unit tests have been enabled on Linux. The wiki [How-To-Test](../How-to-Test.md) article contains the instructions on unit tests for both Windows and Linux.

We now use *Mersenne Twister random engine* and *Boost random distribution functions* in unit tests for both Windows and Linux.

## Python support

Starting from v.1.5 you can use the preview of Python API. See [CNTK v.1.5 Release Notes](./CNTK_1_5_Release_Notes.md) for further instructions.