---
title:   Records 
author:    chrisbasoglu
date:    05/18/2016
ms.author:   cbasoglu
ms.date:   05/18/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# Records

Optional Parameters are a feature that allows additional parameters to be specified for [NDL Functions](./NDL-Functions.md) and [NDL Macros](./NDL-Macros.md). While the optional parameters can be specified for any Function or Macro, they are limited to constant values and the underlying Function must support the passed optional parameters, or there is no effect on the network. When used with a Macro, the Macro will have a local variables defined that matches the optional parameter name and value.

### Parameter initialization

In the network definition given in [NDL Basic concepts](./NDL-Basic-concepts.md) the following parameter matrices are defined: 
```
B0=Parameter(HDim)  
W0=Parameter(HDim, SDim)
```
where ```W0``` is the weight matrix and ```B0``` is the bias matrix. 

Optional parameters are allowed to specify how the functions will be initialized. For instance:
```
B0=Parameter(HDim, init=zero)
W0=Parameter(HDim, SDim, init=uniform)
```
Here the Bias matrix will be zero initialized, and the weight matrix will be initialized with uniform random numbers. Please consult the [Full NDL Function Reference](./Full-NDL-Function-Reference.md) to find which functions accept optional parameters.

### Tagging special values

As an alternate to providing an array of special nodes that are used as features, labels, criteria, etc, optional parameters can be used. So instead of using:
```
FeatureNodes = (features)
LabelNodes = (labels)
CriterionNodes = (CrossEntropy)
EvalNodes = (ErrPredict)
OutputNodes = (Plus2)
```
the same network can be defined as
```
SDim = 784
HDim = 256
LDim = 10

features = Input(SDim, tag="feature")
labels = Input(LDim, tag="label")

L1 = RBFF(features, HDim, SDim)
L2 = RBFF(L1, HDim, HDim)
L3 = RBFF(L2, HDim, HDim)
CE = SMBFF(L3, LDim, HDim, labels, tag="criterion")
Err = ErrorPrediction(labels, CE.F, tag="eval")

OutputNodes = (CE.F)
```
This approach avoids adding elements to the node arrays. Instead it sets the ```tag``` optional parameter on the functions or macros that return the value that fits into the specified category. In this case, since the output node is actually computed inside a macro, we must specify it explicitly.

You will find additional information on *Tags* on the [NDL Special Nodes](./NDL-Special-Nodes.md) page.
