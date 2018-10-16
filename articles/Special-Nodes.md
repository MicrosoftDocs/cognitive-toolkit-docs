---
title:   Special nodes
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   09/15/2016
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   NA
---

# Special nodes

CNTK uses *Special nodes* for automatic back propagation updates of Learnable Parameters and the proper inputs identification. Special nodes can be specified in two different ways:
* Node arrays
* Special Tags
If both methods are used the values are combined.

## Node Arrays

CNTK supports multiple nodes for each type, so the values in types are arrays. However in many there is only a single node for each node type. The array syntax (parenthesis) must be used when setting special nodes, even if there is only one element. If more than one element is included, the entries are comma separated and surrounded by parenthesis. For example:
```
FeatureNodes=(features)
LabelNodes=(labels)
CriterionNodes=(CrossEntropy)
EvalNodes=(ErrPredictOutputNodes, Plus2)
OutputNodes=(ScaledLogLikelihood)
```

## Special Tags

You can use a special *Optional Parameter* named ```tag``` to easily  identify special values in the network. For example:
```
F1=Input(SDim, tag=feature)
L1=Input(LDim, tag=label)
```

The table below contains acceptable tag names and their correspondence to the respective node types:

|Tag name   |Meaning
|:----------|:-------|
|feature    |feature input
|label      |label input
|criterion  |criterion node, top level node
|eval       |evaluation node
|output     |output node

