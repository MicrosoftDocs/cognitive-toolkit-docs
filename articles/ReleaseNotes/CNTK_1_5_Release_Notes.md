---
title:   CNTK_1_5_Release_Notes
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   11/16/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.prod: cntk
ms.devlang:   NA
---

# CNTK_1_5_Release_Notes

The CNTK 1.5 release summarizes the work done on the CNTK codebase by the community and the Microsoft team over the last several months. A big "Thank You" to the community for the great support.

There are many new features, but fixes, enhancements, and improvements in this code base. We also improved our documentation and tutorial effort significantly. We won’t stop here, we will increase this effort! There are many more good things to come, but here is an initial list of new things you can find in the CNTK 1.5 binaries:

## BrainScript

BrainScript is an evolution of our network definition language, and its concepts make it possible to express features not available in NDL (i.e. Sequence-to-Sequence). More about BrainScript: ./BrainScript-Network-Builder

## Sequence to sequence 

CNTK now ships with an initial version of Sequence-to-Sequence modeling, which supports attention as well as beam decoding. 
 
The scenario shows concepts such as 
* Layered Forward-Backward LSTMs 
* Beam Search Decoding 
* Attention model 
 
They are implemented entirely as CNTK computation networks, without Sequence-to-Sequence specific C++ code. 
  
An example is provided that shows how to apply Sequence-2-Sequence models to the task of letter-to-sound translation. This system’s word error rate is very close to the world record for this task. 
  
This initial version still has a number of limitations that will be addressed in the next releases. Notably, for the attention model a maximum length of the input sequence must be known in advance and specified as a parameter. Also, certain operations are not maximally efficient. 
 
See more in https://github.com/Microsoft/CNTK/tree/release/latest/Examples/SequenceToSequence

The model implements the following papers: 
* Sutskever et al. (2014): Sequence to sequence Learning with Neural Networks 
* Vinyals et al. (2014): Grammar as a Foreign Language as well as an addition to use the output state of the input sequence on all inputs of the target  
 
Please note, that the Sequence-to-Sequence model is set up using the CNTK-specific configuration format and not yet exposed through the Python API. 

## BlockMomentum

CNTK 1.5 introduces a new parallelism technique known as *BlockMomentum* that combines the ideas of block updates (as in model averaging) with error residual (1-bit SGD) and takes training scalability to a new level while preserving accuracy.

Read more here: ./Multiple-GPUs-and-machines

## Readers

Significant effort has been put in redesigning and improving our Datareader / Deserializer infrastructure.

* A new CNTK Text Format Reader replacing the UCIFastReader (./BrainScript-CNTKTextFormat-Reader.md)
* Various improvements in the image reader (./BrainScript-Image-reader.md)
* A new deserializer concept and a new speech format deserializer that will replace the current HTKMLFReader in the near future (./Deserializers-and-Transforms)
* The CNTKEval functionality has increased API documentation, insights and additional samples (./CNTK-Evaluation-Overview.md). The CNTK Eval pages have been updated to describe in more detail the overall CNTK capability to evaluate trained models as well as detailing each of the programmatic interfaces for model evaluation (C++ and C#). The pages also include some common pitfalls as well as some suggestions on how to evaluate a model using images.

## Python support
A highly requested feature for easier adoption and experimentation has been a Python API for CNTK. We have created such an API now which can be used to create and execute (train, write, evaluate) deep learning models without the need to learn CNTK's configuration format, in a way that is very similar to other NumPy-based toolkits. It supports both interactive mode, where it can be used directly from Python and interact with Numpy for data input, as well as standalone mode where data is read through a standard data reader and executed through the cntk executable.

This is our first and still experimental release of the Python API, so some extra steps are required to install it:

* Clone CNTK GitHub Repository (you need it to access the Python setup part and related documentation)
* Install Python and Spinx (http://www.sphinx-doc.org/)
* Open documentation and setup Python API as described

> Update: please note that this version of the Python API is superceded by the
> new CNTK v2 Python API, and removed in CNTK version 2.0.beta4.0 and up. A
> detailed description of the Python API can be found in the [CNTK Python API
> Documentation](https://cntk.ai/pythondocs/).
