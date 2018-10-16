---
title:   CNTK FAQ
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   04/05/2017
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   NA
---

# CNTK FAQ

At this page we present the answers to some of the most common questions we get about CNTK and related Subjects.

## What is CNTK?
[CNTK](https://cntk.ai), the Microsoft Cognitive Toolkit, is a framework for deep learning. A Computational Network defines the function to be learned as a directed graph where each leaf node consists of an input value or parameter, and each non-leaf node represents a matrix or tensor operation upon its children. The beauty of CNTK is that once a computational network has been described, all the computation required to learn the network parameters is taken care of automatically. There is no need to derive gradients analytically or to code the interactions between variables for backpropagation.

## Why did Microsoft develop CNTK?
We first created CNTK for ourselves.  CNTK was developed for the fastest training on the biggest data sets.  Many of Microsoft's critical services run on models trained with CNTK. The results were so positive, we wanted to share our toolkit with the world.

## How can I give feedback?
Give us feedback through these [channels](./Feedback-Channels.md).

## Training deep learning models can be time intensive, can CNTK help with this?
For mission critical AI research, we believe efficiency and performance are important criteria. CNTK was designed for peak performance for not only CPUs but also single-GPU, multi-GPU, and multi-machine-multi-GPU scenarios. Additionally, Microsoft’s 1-bit compression technique or Block momentum technique dramatically reduced communication costs -- enabling highly scalable parallel training on a large number of GPUs spanning multiple machines.

## Is CNTK flexible enough for my own network?
In addition to a wide variety of built-in computation nodes, CNTK provides a plug-in architecture allowing users to define their own computation nodes. So if your workload requires special customization, CNTK makes that easy to do. Readers are also fully customizable allowing support for arbitrary input formats.

## What are the key training algorithms supported by CNTK?
Today CNTK supports the following algorithms:
* Feed Forward
* CNN
* RNN
* LSTM
* Sequence-to-Sequence.

## Who are the people behind the CNTK?
CNTK is developed by Microsoft's Technology and Research division.  Additionally, CNTK gets major contributions from nearly all of Microsoft production teams.

## When did work begin on the CNTK?
The development of CNTK has been underway since late 2014.

## Is CNTK only optimized for Speech Recognition Training?
No. CNTK is used in production for the Speech Recognition as well as for Image and Text training.

## How can I use CNTK?
Using CNTK is easy and straightforward.  Here are some ways to get started.
* [Setting up CNTK on your machine](./Setup-CNTK-on-your-machine.md)
* [Tutorials](./Tutorials.md)
* [Examples](./Examples.md)
* [CNTK usage from Brainscript](./CNTK-usage-overview.md)
* [How to contribute to CNTK](./Contributing-to-CNTK.md)

## Why does CNTK randomize the mini-batches after each epoch?
Doing so prevents the same samples from always appearing in a mini-batch together. This leads to improvements in the validation accuracy.

## Can the built-in readers be used train a network model using multiple input files?
Yes.  See the description at [Understanding and Extending Readers](./BrainScript-and-Python---Understanding-and-Extending-Readers.md) and look for the section describing how to "compose several data deserializers" 

## How are sequences handled in CNTK?
See this article [Working with Sequences](https://cntk.ai/pythondocs/sequence.html).

## When looking at dropout, does chosen hidden unit omitted for the entire minibatch as updates takes place only after minibatchSize?
Typically a different set of hidden units are set to 0 for different samples in the same minibatch. For recurrent neural networks, some people constrain to drop same set of hidden units across time for the same sequence.

## Dropout documentation mentions about hidden units, does dropout apply to units in Convolutional Layer if there are multiple of them?
Convolution layer is also a hidden layer if it’s not the last output layer.

## Is there a way to specify different dropout rate to different layers?
In CNTK, you need to explicitly indicate to use dropout. For example, if your original model has `h2=W1*h1` and you want to apply dropout to `h1` you need to change it to `h2=W1*Dropout(h1)`. Currently in BrainScript CNTK only allows to specify one dropout rate for all dropout nodes used in the same model. However, it allows you to select different dropout rate across epochs. In the Python API you can specify a different dropout rate for each layer.

BTW, we are not big fans of using different values for different dropout or model initial values. This would significantly increase the number of hyper parameters to be determined. If this is used in product, this means the model is lack of engineering stability.

## Can we apply weight constraints instead of a L2 regularization term?
Yes, you can compute anything based on weight and add it to your core criterion and use the combined criterion as your training objective.

## Can Python API could read models of BrainScript style trained?
Yes, you are able to read models trained by BrainScript.
