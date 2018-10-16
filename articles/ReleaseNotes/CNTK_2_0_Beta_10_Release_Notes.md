---
title:   CNTK_2_0_Beta_10_Release_Notes
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   02/01/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.prod: cntk
ms.devlang:   NA
---

# CNTK_2_0_Beta_10_Release_Notes

This is a summary of new features delivered with the Beta 10 release of CNTK V.2.0.

## New Examples and Tutorials

* A Python version of the deconvolution layer and image auto encoder example was added ([Example **07_Deconvolution** in *Image - Getting Started*](https://github.com/Microsoft/CNTK/tree/v2.0.beta10.0/Examples/Image/GettingStarted)).
* [Python distributed training example for image classification using AlexNet](https://github.com/Microsoft/CNTK/tree/v2.0.beta10.0/Examples/Image/Classification/AlexNet/Python)
* [Basic implementation of Generative Adversarial Networks (GAN) networks](https://github.com/Microsoft/CNTK/blob/v2.0.beta10.0/Tutorials/CNTK_206_Basic_GAN.ipynb)
* [Training with Sampled Softmax](https://github.com/Microsoft/CNTK/blob/v2.0.beta10.0/Tutorials/CNTK_207_Training_with_Sampled_Softmax.ipynb)

## Python API

The following updates are introduced to Python API:

* Operators can now be implemented in pure Python by means of UserFunctions,
  cf. [here](https://cntk.ai/pythondocs/extend.html)
  * The API is still experimental and subject to change.

* Plotting the CNTK graph
  Plotting a CNTK graph is now as easy as calling

  `cntk.graph.plot(node, 'node.png')`.

  Prerequisites are that the Python module `pydot_ng` is installed (via `pip`)
  and that GraphViz has been installed from graphviz.org and its executable is
  in the system's path.

* API support for object detection using Fast R-CNN was added
  * See the
    [description](../Object-Detection-using-Fast-R-CNN.md)
    and
    [code](https://github.com/Microsoft/CNTK/blob/v2.0.beta10.0/Examples/Image/Detection/FastRCNN/A2_RunCntk_py3.py)
    for the Fast R-CNN example.

* Tensorboard Event Generation
  * Initial support for generating Tensorboard events from the ProgressPrinter
    class was added, cf.
    [here](../Using-TensorBoard-for-Visualization.md)

## Bug fixes
* Speed up TimesNodeBase for sparse by avoiding unroll. This improves speed of
  CrossEntropyWithSoftmax and ClassificationError for sparse labels. The
  language understanding example
  ([LanguageUnderstanding.py](https://github.com/Microsoft/CNTK/blob/v2.0.beta10.0/Examples/LanguageUnderstanding/ATIS/Python/LanguageUnderstanding.py))
  got four times faster now.

## CNTK Evaluation library. NuGet package

A new set of NuGet Packages is provided with this Release. 

**IMPORTANT!** In Visual Studio *Manage NuGet Packages* Window change the default option *Stable Only* to *Include Prerelease*.
After selecting the appropriate NuGet package to install, use the version selector on the right to explicitly select package version `2.0.0-beta10`.
