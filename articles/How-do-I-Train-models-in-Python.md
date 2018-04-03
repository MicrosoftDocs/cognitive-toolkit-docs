---
title:   How do I train models in Python
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   04/12/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   python
---

# How do I train models in Python

 * [Load pre-trained checkpointed model and continue retraining](#load-pre-trained-checkpointed-model-and-continue-retraining)?
 * [Relate alpha, beta1, beta2 and epsilon to learning rate and momentum in adam_sgd](#relate-alpha-beta1-beta2-and-epsilon-to-learning-rate-and-momentum-in-adamsgd)?
 * [Train two or more models jointly](#train-two-or-more-models-jointly)?
 * [Train with a weighted loss](#train-with-a-weighted-loss)?
 * [Train a multilabel classifier in Python](#train-a-multilabel-classifier-in-python)? 
 * [Train an Image auto encoder using Deconvolution and Unpooling](#train-an-image-auto-encoder-using-deconvolution-and-unpooling)?
 * [Object Detection using Fast R-CNN](#train-object-detection-using-fast-r-cnn)?
 * [Build your own image classifier using Transfer Learning](#train-an-image-classifier-using-transfer-learning)?

## Load pre trained checkpointed model and continue retraining

You have a CNTK `trainer` object and save a checkpoint file.
  
    checkpoint_file = "mModel_checkpointed.dnn"
    trainer.save_checkpoint(checkpoint_file)

At a later time I load the `".dnn"` file as my model

    mymodel.load_model(checkpoint_file)

When you restart training and it would load the model but not the weights. To be able to restore training from the previously checkpointed model. You need to use [`trainer.restore_from_checkpoint`](https://cntk.ai/pythondocs/cntk.trainer.html?highlight=restore_from_checkpoint#cntk.trainer.Trainer.restore_from_checkpoint) instead, to recreate the trainer and learners. One important thing is that in your Python script the order of network creation (not only structure! but the order in which you create your nodes) should stay the same at the point when you create a checkpoint and when you restore from it.

## Relate alpha, beta1, beta2 and epsilon to learning rate and momentum in adam_sgd

* Alpha is the learning_rate
* Beta1 is momentum parameter
* Beta2 is variance_momentum parameter

## Train two or more models jointly

There are two cases that this can arise. One is multitask learning the other is you have one processing pipeline for some part of your input and another pipeline for the other. 

  * Multitask learning is when you have multiple criteria you want your network to be good at. The standard way to deal with this is to construct a single number out of all these criteria. For example
```python
cross_entropy = cross_entropy_with_softmax(predictions, labels)
reconstruction_error = squared_error(reconstruction, features)
combined_loss = 0.3 * reconstruction_error + cross_entropy
```
  * Multiple pipelines such as the one below are also tricky 
```python
q_embed = qmodel(question)
a_embed = amodel(answer)
```

The trainer takes a single function whose parameters should be modified by the training procedure. How can we pass the union of `qmodel` and `amodel` or the union of `cross_entropy` and `reconstruction_error`? The answer is to find a point where all the parameters are part of the same function. That happens at the very least in the final loss.
i.e. `loss.parameters` contains all parameters the loss depends on. Typically however our model should not 
include the loss, as it only makes sense for training. Therefore a better approach is to use combine to create a combined model
```python 
final_model = combine(predictions, reconstruction)
```
For the separate pipeline case there is probably a place where everything gets combined
```python 
qa_score = score(q_embed,a_embed)
```
then `qa_score` can play the role of `final_model` above.

## Train with a weighted loss

The specification of a per-example weight in the loss is as simple as
```python
weight = input_variable((1))
weighted_loss = weight * loss
```
where `loss` is any builtin or user-defined loss function. Of course during training each minibatch will need to have a mapping from weight to actual values (one for each example).

## Train a multilabel classifier in Python

For multilabel classification you should avoid using CrossEntropy as it can only handle input vectors that sum to 1. A sensible alternative is to use hamming loss.

```python
def my_hamming_loss:
    y = placeholder()
    p = placeholder()
    hammingLoss = reduce_sum (not_equal (y, (greater (p, 0.5))))
    return hammingLoss 
```
which you then can call as hammingLoss(labels, prob) and pass as the error metric to the Trainer.

## Train an Image Auto Encoder Using Deconvolution And Unpooling

There are instructions [here](./Image-Auto-Encoder-Using-Deconvolution-And-Unpooling.md).

## Train Object Detection using Fast R CNN

There are instructions [here](./Object-Detection-using-Fast-R-CNN.md).

## Train an image classifier using Transfer Learning

There are instructions [here](./Build-your-own-image-classifier-using-Transfer-Learning.md)
