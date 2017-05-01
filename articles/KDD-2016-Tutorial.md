---
title:   KDD 2016 Tutorial
author:    chrisbasoglu
date:    11/07/2016
ms.author:   cbasoglu
ms.date:   11/07/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   python 
---

# KDD 2016 Tutorial

## CNTK: Microsoft's Open-Source Deep-Learning Toolkit
KDD 2016 Hands-On Tutorial by Frank Seide and Amit Agarwal, Microsoft

[Slides used in the tutorial](https://cntk.ai/kdd/Slides.zip)

**Note: You will need to make two large downloads**, the CNTK binary (~300 MB) and the CIFAR-10 data set (170 MB) and install the CNTK binaries and all required dependencies on your machine. 
We recommend to do this beforehand, e.g. from your hotel room. See [below](#prerequisites-download-and-setup-instructions) for instructions. 

Train neural networks like Microsoft product groups! This tutorial will introduce the Microsoft Cognitive Toolkit, or CNTK, Microsoft’s scalable open-source deep-learning toolkit for Windows and Linux. CNTK is a powerful computation-graph based deep-learning toolkit for training and evaluating deep neural networks. Microsoft product groups use CNTK, for example to create the Cortana speech models and web ranking.

### Target Audience and Expectation
This tutorial is targeted at current or future deep-learning practitioners looking for a tool that is **easy to use** yet **efficient** and **scalable** across **multi-machine GPU** clusters for real-world workloads.

The tutorial assumes basic knowledge of deep learning. Participants will get to understand CNTK's core concepts and usage, and practice to run neural-network trainings with CNTK for image recognition and text processing. The tutorial will be a starting point for solving your own real-world deep-learning task with CNTK.

### Prerequisites, Download, and Setup Instructions
To do the hands-on lab sessions in the tutorial, a laptop or remote computer with 64bit Windows 8.1+ or Linux is required, and a CUDA-capable GPU is recommended (the faster the better). 
If your machine is running Mac-OS, you can run Linux inside a docker container and perform the installation/setup described below inside this docker container. 

You will need to download and install the CNTK binary package, the CIFAR-10 set, and the tutorial files, in total about 500 MB. Ideally do this beforehand.

* **Download and install the CNTK binaries**: 

[This page](./Setup-CNTK-on-your-machine.md) explains you the different alternatives to install the Microsoft Cognitive Toolkit 
onto your machine. For the scope of this tutorial an installation based of a binary CNTK installation is sufficient. You 
will not need to build CNTK from the source code. for setup instructions. You can just follow the instructions 
for downloading a binary install package from that page. 

In any case make sure that the CNTK executables are included in your environment.

* **Download the CIFAR-10 set**: For the CIFAR-10 set, please download this file: http://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz

* **Download the Tutorial files** (~12 MB) by opening [this link](https://github.com/Microsoft/CNTK/blob/fseide/kdd/Tutorials/CNTK_HandsOn_KDD2016.zip) and clicking the Download button.

### Do the hands-on tutorials online

The two hands-on tutorials have been posted as Wiki pages that can be followed along:

* [Image Recognition on CIFAR-10](./Hands-On-Labs-Image-Recognition.md): Image recognition on CIFAR-10 with convolutional and Residual Nets
* [Language Understanding with ATIS](./Hands-On-Labs-Language-Understanding.md): Slot tagging and intent classification with recurrent networks

## ABSTRACT
This tutorial will introduce the Microsoft Cognitive Toolkit, or CNTK, Microsoft’s cutting-edge open-source deep-learning toolkit for Windows and Linux. CNTK is a powerful computation-graph based deep-learning toolkit for training and evaluating deep neural networks. Microsoft product groups use CNTK, for example to create the Cortana speech models and web ranking. CNTK supports feed-forward, convolutional, and recurrent networks for speech, image, and text workloads, also in combination. Popular network types are supported either natively (convolution) or can be described as a CNTK configuration (LSTM, sequence-to-sequence). CNTK scales to multiple GPU servers and is designed around efficiency.
This tutorial will give an overview of CNTK's general architecture and describe the specific methods and algorithms used for automatic differentiation, recurrent-loop inference and execution, memory sharing, on-the-fly randomization of large corpora, and multi-server parallelization. We will then show how typical uses looks like for relevant tasks like image recognition, sequence-to-sequence modeling, and speech recognition. 

### OUTLINE
The tutorial will cover these topics:
* What is CNTK?
  * Computational network introduction
* How does a typical use of CNTK look like?
  * Defining the Computational Network
  * Configuring data I/O
  * SGD hyper-parameters
  * Typical workflows
* Deep Dive into specific technologies
  * Implicit handling of time
  * Minibatching of variable-length sequences
  * Data-parallel training
* Hands-on examples, including
  * Image recognition (AlexNet, ResNet)
  * Text processing (ATIS)
* Preview: CNTK Library APIs for Python and C++

### SPEAKER BIOGRAPHIES
**Frank Seide**, a native of Hamburg, Germany, is a Senior Researcher at Microsoft Research. His current research focus is on deep neural networks for conversational speech recognition; together with co-author Dong Yu, he was first to show the effectiveness of deep neural networks for recognition of conversational speech. Throughout his career, he has been interested in and worked on a broad range of topics and components of automatic speech recognition, including spoken-dialogue systems, recognition of Mandarin Chinese, and, particularly, large-vocabulary recognition of conversational speech with application to audio indexing, transcription, and speech-to-speech translation. His current focus is Microsoft's CNTK deep-learning toolkit.  
**Amit Agarwal** is a principal software engineer at Microsoft’s Technology and Research division. His current focus is on building CNTK, Microsoft’s large scale distributed deep learning platform, to enable unprecedented scale, speed and capacity for training massive deep learning models on enormous datasets, used in a wide gamut of speech, image and text related deep learning tasks at Microsoft and in the community. Amit Agarwal worked on a wide range of Microsoft products and at Mentor graphics. He holds 7 patents related to heterogeneous and GPU programming.

### REFERENCES
[1]	Amit Agarwal, Eldar Akchurin, Chris Basoglu, Guoguo Chen, Scott Cyphers, Jasha Droppo, Adam Eversole, Brian Guenter, Mark Hillebrand, T. Ryan Hoens, Xuedong Huang, Zhiheng Huang, Vladimir Ivanov, Alexey Kamenev, Philipp Kranen, Oleksii Kuchaiev, Wolfgang Manousek, Avner May, Bhaskar Mitra, Olivier Nano, Gaizka Navarro, Alexey Orlov, Hari Parthasarathi, Baolin Peng, Marko Radmilac, Alexey Reznichenko, Frank Seide, Michael L. Seltzer, Malcolm Slaney, Andreas Stolcke, Huaming Wang, Yongqiang Wang, Kaisheng Yao, Dong Yu, Yu Zhang, Geoffrey Zweig (in alphabetical order), "An Introduction to Computational Networks and the Computational Network Toolkit", Microsoft Technical Report MSR-TR-2014-112, 2014.  
[2]	"CNTK," https://docs.microsoft.com/en-us/cognitive-toolkit/
