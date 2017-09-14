---
title:   MultiGPU Support with Keras
author:    duli2012
ms.author:  duli1
ms.date:   09/15/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

#CNTK Multi-GPU Support with Keras
Since CNTK 2.0, Keras can use CNTK as its back end, more details can be found [here](./using-cntk-with-keras.md). 
As stated in [this article](./Multiple-GPUs-and-machines), CNTK supports parallel training on multi-GPU and multi-machine. This article elaborates how to conduct
parallel training with Keras by walking through an example.

**Step 1.** data partitioning
Since Keras does not provide data partitioning API, users must do it according to their requirements and design choices. Below is the function we used to partition data
in this example.

**Step 2.**, create a Keras model
As you see in the following code snippet, this step just uses Keras to build a model. 

**Step 3.**, construct a distributed trainer
This step is a bit tricky, users need to explicitly construct a CNTK distributed trainer and provide it to Keras model generate in last step. Here, the trainer
uses [BlockMomentumSGD algorithm](./Multiple-GPUs-and-machines#6-block-momentum-sgd). User can easily choose other parallel SGD algorithms support by CNTK.

**Step 4.** train the model
Similar to Step 2, the standard Keras fit() API is used to train the model.

The complete example can be found [here](./mnist_mlp_distributed.py)
