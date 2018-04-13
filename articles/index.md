---
title:   The Microsoft Cognitive Toolkit
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:  01/22/2017
ms.custom:   cognitive-toolkit
ms.topic:   overview
ms.devlang:   NA
---

# The Microsoft Cognitive Toolkit

The Microsoft Cognitive Toolkit (CNTK) is an open-source toolkit for commercial-grade distributed deep learning. It describes neural networks as a series of computational steps via a directed graph. CNTK allows the user to easily realize and combine popular model types such as feed-forward DNNs, convolutional neural networks (CNNs) and recurrent neural networks (RNNs/LSTMs). CNTK implements stochastic gradient descent (SGD, error backpropagation) learning with automatic differentiation and parallelization across multiple GPUs and servers.

[This video](https://youtu.be/9gDDO5ldT-4) provides a high-level overview of the toolkit. In addition, Microsoft offers an introductory course to deep learning with CNTK, [Deep Learning Explained](https://www.edx.org/course/deep-learning-explained-microsoft-dat236x-0).

The latest release of CNTK is [2.5.1](./ReleaseNotes/CNTK_2_5_1_Release_Notes.md).

CNTK can be included as a library in your Python, C#, or C++ programs, or used as a standalone machine-learning tool through its own model description language (BrainScript). In addition you can use the CNTK model evaluation functionality from your Java programs.

CNTK supports 64-bit Linux or 64-bit Windows operating systems. To install you can either choose pre-compiled binary packages, or compile the toolkit from the source provided in [GitHub](https://github.com/Microsoft/CNTK).

<br/>
<img src="./pictures/ONNX_logo_main.png" width="200">

CNTK is also one of the first deep-learning toolkits to support the Open Neural Network Exchange [ONNX](https://onnx.ai) format, an open-source shared model representation for framework interoperability and shared optimization. Co-developed by Microsoft and supported by many others, ONNX allows developers to move models between frameworks such as CNTK, Caffe2, MXNet, and PyTorch.


The latest release of CNTK supports ONNX v1.0.

Learn more about ONNX [here](https://github.com/onnx/onnx).

