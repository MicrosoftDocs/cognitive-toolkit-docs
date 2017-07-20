---
title:   CNTK_2_0_Beta_3_Release_Notes
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   11/14/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK_2_0_Beta_3_Release_Notes

This is a summary of new features delivered with the Beta 3 release of CNTK V.2.0.

## Integration with NVIDIA NCCL

This Release introduces the integration of CNTK and [NVIDIA NCCL](https://github.com/NVIDIA/nccl), a stand-alone library of standard collective communication routines, such as all-gather, reduce, broadcast, etc., that have been optimized to achieve high bandwidth over PCIe. 

NCCL library supports Linux system only. NCCL can be enabled for those who build CNTK from the source code. See how to enable NCCL [here](../Setup-CNTK-on-Linux.md#cudnn).

## CNTK Evaluation library. NuGet package

This Release features NuGet package for CNTK Evaluation library. **IMPORTANT!** In Visual Studio *Manage NuGet Packages* Window change the default option *Stable Only* to *Include Prerelease*. Otherwise the package will not be visible. The Package version should be ```2.0-beta3```.

## Stability Improvements
We continue fine tuning new features and fixing different bugs - thank you once again for the constant feedback. You are not required to adopt your code or models to take an advantage of these improvements.
