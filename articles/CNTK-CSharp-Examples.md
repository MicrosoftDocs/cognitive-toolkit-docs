---
title:   Model evaluation examples
author:    liqun
ms.author:   liqun
ms.date:  07/31/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   csharp
---

# CNTK C#/.NET API training examples

## Overview
CNTK repository contains [examples](https://github.com/Microsoft/CNTK/tree/master/Examples/TrainingCShape) using CNTK C# API to build, train, and evaluate CNTK neural network models. 

#### LogisticRegression
A hello-world example to train and evaluate a logistic regression model using C#/API. See [CNTK 101: Logistic Regression and ML Primer](https://github.com/Microsoft/CNTK/blob/master/Tutorials/CNTK_101_LogisticRegression.ipynb) for more details.
#### MNISTClassifier 
This class shows how to build and train a classifier for handwritting data (MNIST).  
#### CifarResNetClassifier 
This class shows how to do image classification using ResNet.
The model being built is a lite version of [Deep Residual Learning for Image Recognition](https://arxiv.org/abs/1512.03385). See [Python Tutortials](https://github.com/Microsoft/CNTK/blob/master/Tutorials/CNTK_201B_CIFAR-10_ImageHandsOn.ipynb) for more details.
#### TransferLearning 
This class demonstrates transfer learning using a pretrained ResNet model. 
See [Python Tutortials](https://github.com/Microsoft/CNTK/blob/master/Tutorials/CNTK_301_Image_Recognition_with_Deep_Transfer_Learning.ipynb) for more details. 
#### LSTMSequenceClassifier 
This class shows how to build a recurrent neural network model from ground up and how to train the model.


