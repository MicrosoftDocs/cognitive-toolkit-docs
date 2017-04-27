---
title:   CNTK_2_0_Beta_9_Release_Notes
author:    chrisbasoglu
date:    01/25/2017
ms.author:   cbasoglu
ms.date:   01/25/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK_2_0_Beta_9_Release_Notes

This is a summary of new features delivered with the Beta 9 release of CNTK V.2.0.

## Breaking changes

There is a **breaking change** in this release:

* Image Reader is updated with random area option (useful for Inception-style networks), and color transform option.

## New and updated features

* Lambda rank and NDCG at 1 are accessible from Python for real this time.
* Changes in Learner API: for learners that use momentum (`momentum_sgd`, `nesterov_sgd` and `adam_sgd`) it is now possible to specify if the momentum should be applied  in the regular fashion or as a unit-gain filter (default). For more details on the unit-gain momentum, please refer to this [section in the Wiki](../BrainScript-SGD-Block.md#converting-learning-rate-and-momentum-parameters-from-other-toolkits). 
* No more partial minibatches at the sweep boundary. Now minibatches are allowed to transparently cross the sweep boundary. `epoch_size` parameter for all hyperparameter schedules now defaults to full data sweep (i.e., by default, hyperparameters change their values on the sweep by sweep basis).

## New Examples and Tutorials

* Deconvolution layer and image auto encoder example using deconvolution and unpooling ([Example **07_Deconvolution** in *Image - Getting Started*](https://github.com/Microsoft/CNTK/tree/v2.0.beta9.0/Examples/Image/GettingStarted)).
* [Basic autoencoder with MNIST data](https://github.com/Microsoft/CNTK/blob/v2.0.beta9.0/Tutorials/CNTK_105_Basic_Autoencoder_for_Dimensionality_Reduction.ipynb).
* [LSTM Timeseries with Simulated Data (Part A)](https://github.com/Microsoft/CNTK/blob/v2.0.beta9.0/Tutorials/CNTK_106A_LSTM_Timeseries_with_Simulated_Data.ipynb). (More will come in the next Releases) 

## Python API

The following updates are introduced to Python API:

* Default Python version for binary installation script was changed to **3.5** for both Windows and Linux. As before you can manually select version 2.7, 3.4, or 3.5 during the installation. Please see [binary and source setup](../Setup-CNTK-on-your-machine.md) instructions to find out about how to select Python version.
* [Docker Hub Runtime Image](../CNTK-Docker-Containers.md) will also contain Python v. 3.5.
* Lambda rank and NDCG at 1 are now accessible from Python.
* Preliminary version of the training session API for distributed learning is exposed in Python.

## CNTK Evaluation library. NuGet package

A new set of NuGet Packages is provided with this Release. 

**IMPORTANT!** In Visual Studio *Manage Nuget Packages* Window change the default option *Stable Only* to *Include Prerelease*. Otherwise the packages will not be visible. The Package version should be ```2.0-beta9```.