---
title:   AAAI 2018 Tutorial
author:    tangyuq
ms.author:   yuqtang
ms.date:   02/03/2018
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.prod:  cntk
ms.devlang:   NA
---

# AAAI 2018 Tutorial

## Abstract

Deep learning is rapidly transforming the landscape of Artificial Intelligence opening up a new era of possibilities in applications. This tutorial will introduce the Microsoft Cognitive Toolkit (CNTK) — a cutting-edge open-source deep-learning toolkit for Windows and Linux. CNTK is a powerful computation-graph based toolkit for training deep neural networks and inference. Microsoft product groups use CNTK, for example to create the Cortana speech models and web ranking. CNTK supports feed-forward, convolutional, and recurrent networks for speech, image, and text workloads, also in combination. Popular network types (e.g. convolution, LSTM and etc.) are supported either natively or can be implemented using CNTK operators, layers and user-defined lambda functions. CNTK scales to multiple GPU servers and is designed around efficiency. This tutorial will cover basic deep learning models, such as feed-forward networks, convolution, recurrence. We will also discuss the GAN (Generative Adversarial Network) and deep reinforcement learning. In addition, we will dive deep into the underlying CNTK specific training techniques for scalable training over large data sets, such as 1-bit SGD, variable length sequence packing, parallel and distributed per-sample training. Microsoft Azure cloud instances will be provided for the tutorial audiences during the course to practice deep networks programming in CNTK.

## Presentation:
You can find the link to the presentation [here](https://cntk.ai/Tutorials/AAAI18/CNTK%20Tutorial_AAAI_Feb_2018_final.pdf).

## Tentative Schedule

*Duration*: Half day (4 hours). 

Session 1

> •	Introduction

> • A concise introduction to Deep Neural Networks

> •	CNTK: What is Microsoft Cognitive toolkit? Why use Microsoft Cognitive Toolkit


Session 2

> • CNTK workflow: Data reading and augmentations, Modeling (MLP, CNN, RNN), Training-Test-Eval workflow

> •	Image applications: ResNet, Inception, ConvNet/Emotion, Faster R-CNN, Segmentation, Neural style, GAN/Pixel CNN, etc. 


Session 3

> •	Parallel training: 1-bit SGD, Block momentum, variable sized mini-batch

> •	Reinforcement learning in simulated environment


Session 4

> •	Video applications

> • Other topics if time is available: Scale CNTK runs in Spark


Conclusion and QA



## Installation instructions

Install Cognitive Toolkit (CNTK) from the [GitHub location](./Setup-CNTK-on-your-machine.md) on either Windows or Linux Machine

We recommend you locally install the toolkit and Git clone the repository. However, for those who do not want to do so you can try out some of the tutorials at our [CNTK Azure Notebooks](http://notebooks.azure.com/cntk/libraries/tutorials) location for free. To run the CNTK notebooks at Azure, please sign up for a free Microsoft account.

## About presenters

**Yuqing Tang**

Microsoft Research
One Microsoft Way, 
Redmond, WA 98052

Phone: 1 (425) 421-1756
Email: yuqtang@microsoft.com

Yuqing Tang is an Applied Scientist II at Microsoft AI & Research. He is a member of the Microsoft deep learning CNTK toolkit team. Before joining Microsoft, he was at the Robotics Institute at Carnegie Mellon University first in 2012 as a postdoctoral fellow and later in 2015 as a project scientist. He has published more than 40 technical papers in Artificial Intelligence. 

**Sayan Pathak**

Microsoft Research
One Microsoft Way, 
Redmond, WA 98052

Phone: 1 (425) 538-7386
Email: sayanpa@microsoft.com 

Sayan Pathak is a Principal Engineer and Machine Learning Scientist in the Advanced Technology Group at Microsoft Research. He received MS and PhD from University of Washington in 1996 and 2000, respectively.  He has published and commercialized cutting edge computer vision and machine learning technology to big data problems applied to medical imaging, neuroscience, computational advertising and social network domains. He has over 20 journal papers published in IEEE, ACM, Nature, Nature Neuroscience, Neuron and conferences (IEEE, SPIE, SIIM, Asonam). He has also been a faculty at the University of Washington for 10 years and at the Indian Institute of Technology, Kharagpur for over 4 years. He has been principal investigator on several US National Institutes of Health (NIH) grants. 
