---
title:   Compatible dimensions in reader and config
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   10/04/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   NA
---

# Compatible dimensions in reader and config

There are two typical cases where you might see this error message. The underlying reason is that the reader config and the training config do not agree. 

The first typical case is that when you modify a working config file to change the dimensionality of the output (e.g. take a config that works for a 7 class problem and make a new one that should work for a 5 class problem) you may encounter the error message 
```
NotifyFunctionValuesMBSizeModified: labels InputValue operation had its row dimension 7 changed by the reader to 5
```
This is most likely due to only updating the network definition part of your config file but not your reader definition.
Please double-check:

* all [`Input{}`](./Inputs.md) nodes have corresponding streams in the reader section with the same name
* the dimensions match

A second case is when you provide a wrong hint to the reader by saying 
```
featureNodes = (z)
```
and `z` is not an actual input but an expression.
