---
title:   CNTK Examples
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   06/01/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK Examples

The [Tutorials/](https://github.com/Microsoft/CNTK/blob/master/Tutorials/) and
[Examples/](https://github.com/Microsoft/CNTK/blob/master/Examples/) folders
contain variety of example configurations for CNTK networks using both the
Python API and BrainScript.
The examples are structured by topic into Image, Language Understanding,
Speech, and so forth. To get started with CNTK we recommend the tutorials in
the `Tutorials` folder.

## Python Examples

The best way to learn about the APIs is to look at the following examples in the [CNTK clone root]/Examples directory:
* [MNIST:](https://github.com/Microsoft/CNTK/blob/v2.0/Examples/Image/Classification/MLP/Python/SimpleMNIST.py) A fully connected feed-forward model for classification of MNIST images. (follow the instructions in Examples/Image/DataSets/MNIST/README.md)
* [TrainResNet_CIFAR10:](https://github.com/Microsoft/CNTK/blob/v2.0/Examples/Image/Classification/ResNet/Python/TrainResNet_CIFAR10.py) An image classification ResNet model for training on the CIFAR image dataset. (follow the instructions in Examples/Image/DataSets/CIFAR-10/README.md to get the CIFAR dataset and convert it to the CNTK supported format)
* [SequenceClassification:](https://github.com/Microsoft/CNTK/blob/v2.0/Examples/SequenceClassification/SimpleExample/Python/SequenceClassification.py) An LSTM sequence classification model for text data.
* [Sequence2Sequence:](https://github.com/Microsoft/CNTK/blob/v2.0/Examples/SequenceToSequence/CMUDict/Python/Sequence2Sequence.py) A sequence to sequence grapheme to phoneme translation model that trains on the CMUDict corpus.
* [NumpyInterop](https://github.com/Microsoft/CNTK/blob/v2.0/Tutorials/NumpyInterop/FeedForwardNet.py) - NumPy interoperability example showing how to train a simple feed-forward network with training data fed using NumPy arrays.
* [LanguageUnderstanding](https://github.com/Microsoft/CNTK/blob/v2.0/Examples/LanguageUnderstanding/ATIS/Python/LanguageUnderstanding.py) - Language Understanding.
* [Video](https://github.com/Microsoft/CNTK/blob/v2.0/Examples/Video/GettingStarted/Python/Conv3D_UCF11.py) - Basic 3D convolution networks for deep learning on video tasks.

An overview on all examples and tutorials is also provided by the
[Cognitive Toolkit Model Gallery](http://www.microsoft.com/en-us/cognitive-toolkit/features/model-gallery/) page.

## Evaluation Examples

The [CNTK Eval Examples](./CNTK-Eval-Examples.md) page provides examples demonstrating how to evaluate pre-trained models using C++, C#/.NET, Python, and Java.