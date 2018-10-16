---
title:   WWW 2017 Tutorial
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   04/03/2017
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   NA
---

# WWW 2017 Tutorial

## Abstract

Deep Neural Networks (DNNs) have revolutionized the way machines understand language and generate various responses such as answering questions, translating languages, and comparing document corpora. At the heart lies core techniques that fall into the bucket of sequence understanding. Dealing with variable size sequences of different lengths in DNNs requires deep understanding of creating such networks which can be daunting to many scientists and engineers. This tutorial will focus on introducing core concepts, end-to-end recipes, and key innovations facilitated by the cross-platform fully open-sourced Cognitive Toolkit (formerly called CNTK) with superior scalability (up to 1000 GPUs) for very large data corpora. Specifically, we will present tutorials on basic sequence understanding, intermediate sequence-to-sequence understanding (both with and without attention), and the advanced Reasoning Network (ReasoNet) which has achieved industry leading results in the area of reading comprehension.

## Presentation:
You can find the link to the presentation [here](https://cntk.ai/Tutorials/WWW2017/WWW_2017_CNTK_Tutorial_Language_and_sequence.pdf)

## Tentative Schedule

*Duration*: Half day (3.5 hours). 

Motivation (15 min)

> •	Introduction to Cognitive Toolkit (Architecture, flexibility & extensibility)

> •	Why Cognitive Toolkit 

> •	Scalability, treat Windows and Linux as first class citizens, C++ and Python API, RNN advantages due to dynamic axis.

Basics (45 min + 45 min)

> •	Tensor Operations, Data readers, built-in data augmentation, Optimizations 

> •	RNN/LSTM modules – Embeddings, sequence tagging / classification

> •	[Sequence classification](https://github.com/Microsoft/CNTK/blob/v2.0.beta15.0/Tutorials/CNTK_202_Language_Understanding.ipynb) (45 min) – Hands-on Lab

Break (15 min)

Advanced 

> *	[Sequence to Sequence with Attention](https://github.com/Microsoft/CNTK/blob/v2.0.beta15.0/Tutorials/CNTK_204_Sequence_To_Sequence.ipynb) (45 min) – Hands-on Lab

> *	[ReasoNet](https://github.com/Microsoft/CNTK/blob/penhe/reasonet_tutorial/Tutorials/CNTK_302_ReasoNet.ipynb) (45 min) – code walk through. Note: This code is in the process of being migrated to master.

Scale-out (15min)

> •	Enable parallel training

*	Conclusion and QA (15 min)



## Installation instructions

Install Cognitive Toolkit (CNTK) from the [GitHub location](./Setup-CNTK-on-your-machine.md) on either Windows or Linux Machine

We recommend you locally install the toolkit and Git clone the repository. However, for those who do not want to do so you can try out some of the tutorials at our [CNTK Azure Notebooks](http://notebooks.azure.com/cntk/libraries/tutorials) location for free. 

## About presenters

**Sayan Pathak**

Microsoft Research
One Microsoft Way, 
Redmond, WA 98052

Phone: 1 (425) 538-7386
Email: sayanpa@microsoft.com 

Sayan Pathak is a Principal Engineer and Machine Learning Scientist in the Advanced Technology Group at Microsoft Research. He received MS and PhD from University of Washington in 1996 and 2000, respectively.  He has published and commercialized cutting edge computer vision and machine learning technology to big data problems applied to medical imaging, neuroscience, computational advertising and social network domains. He has over 20 journal papers published in IEEE, ACM, Nature, Nature Neuroscience, Neuron and conferences (IEEE, SPIE, SIIM, Asonam). He has also been a faculty at the University of Washington for 10 years and at the Indian Institute of Technology, Kharagpur for over 4 years. He has been principal investigator on several US National Institutes of Health (NIH) grants. 

**William Darling** 

Microsoft AI and Research
Microsoft Search Technology Center, 
Munich

Phone: +49 (89) 31766089
Email: wdarling@microsoft.com 

William Darling is an Applied Scientist in Microsoft AI and Research. He received an H.B.Sc. in Physics and Computer Science from Trent University in 2004, LL.B. and B.C.L. degrees from McGill University in 2007, and a Ph.D. focusing on Machine Learning and Topic Modeling from the University of Guelph in 2012. He is currently working on the AI Platform Team for Bing with a focus on deep recurrent neural network models and CNTK.  He has published and commercialized technology in the areas of NLP, machine learning, and information retrieval.

**Pengcheng He**

Microsoft Research
One Microsoft Way, 
Redmond, WA 98052

Pengcheng is a Senior Developer with Microsoft AI and Research.

