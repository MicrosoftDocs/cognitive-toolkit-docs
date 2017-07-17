---
title:   CNTK_1_6_Release_Notes
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   07/29/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK_1_6_Release_Notes

This is a summary on what's new in CNTK 1.6 Binary Release. Apart from many bug fixes we have the following new features.

## "With 1bit-SGD: no" message in cntk.exe output on Windows for 1bit-SGD edition

1bit-SGD on Windows edition of CNTK v.1.6 produces the message "With 1bit-SGD: no" in the "Build info" section of the output (usually the first section of the console output). The easiest way to produce this output is to call cntk.exe from Windows command prompt without supplying any additional parameters.

IMPACT: There is NO impact on CNTK 1bit-SGD functionality. That means, that 1bit-SGD edition should function as expected with all 1bit-SGD and related functionality enabled.

The reason of the message is some misconfiguration in the CNTK internal build system. It affects only the console output described above and does NOT affect any other functionality. The wrong output message will be fixed in the next release of CNTK.

## MKL Support

Intel Math Kernel Library (MKL) is now the main Math library for CNTK. Starting from v1.6 CNTK binary releases are MKL-based. 

You can still compile CNTK code with ACML but please be aware that we plan to phase out the support for it.

## CNTK Model Evaluation library

V.1.6 features CNTK Evaluator both for Windows and Linux. 

For Windows you can get CNTK Eval is a NuGet Package or directly from the Binary Distribution. Please note, that this time the NuGet package is MKL-based.

Linux CNTK Evaluator is included in Linux binary distribution.

See more on the CNTK Model Evaluation in the [correspondent section](../CNTK-Evaluation-Overview.md). 

## Deconvolution and Unpooling

CNTK now supports Deconvolution and Unpooling. See the usage example in the [Network number 4 in MNIST Sample](https://github.com/Microsoft/CNTK/blob/master/Examples/Image/MNIST/README.md). (All Examples are included in the Binary Distribution).

## Support of OpenCV 3.1

In this version ImageReader was compiled against OpenCV v3.1. This fixed some race conditions at the beginning of the first epoch due to invalid implementation of static initializers in OpenCV v3.0 in Windows.

## Python support

Starting from v.1.5 you can use the preview of Python API. See [CNTK v.1.5 Release Notes](./CNTK_1_5_Release_Notes.md) for further instructions.
