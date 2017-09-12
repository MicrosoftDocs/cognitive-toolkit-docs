---
title:   Variables
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   05/18/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---
# Variables

Variables are defined in NDL when they appear on the left of an equal sign ```=```. From that point on that variable name will be associated with the value it was assigned. Variables are **immutable**, thus assigning new values to an existing variable is not supported.

Variable names may be any alphanumeric sequence that starts with a letter. A variable can contain a matrix or scalar value.

## Reserved words

Any name that is a function name is reserved and can not be used for a variable. The special node names below are also  reserved and can not be used as variable names.
```
FeatureNodes
LabelNodes
CriteriaNodes
EvalNodes
OutputNodes
```

## Variable names with dots

When it is necessary to access a variable that is defined in a macro it can be accessed using dot syntax. If the Macro ``FF`` is called from the following code:
```
L1 = FF(features, HDim, SDim)
```
and the Macro ```FF`` is defined as below:
```
FF(X1, W1, B1)
{
	T=Times(W1,X1);
	FF=Plus(T, B1);
}
```
then it is possible to get what was the return value of ```Times(W1,X1)``` Function call *before* Function ```Plus(T, B1)``` was called. To do that one needs to get the value of ```L1.T`` variable.

Dot syntax uses variable names defined within Macro definitions, thus it is important to have all required Macro definitions available to use this technique. 

For nested Macros it is possible to use multi-layer dot syntax.

 


