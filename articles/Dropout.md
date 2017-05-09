---
title:   Dropout 
author:    chrisbasoglu
date:    08/15/2016
ms.author:   cbasoglu
ms.date:   08/15/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# Dropout 

Dropout function.

    Dropout (x)

## Parameters

* `x`: the input to apply the dropout function to

Note: the dropout rate is not a parameter to this function, but instead specified in the `SGD` section.

## Return Value

`Dropout()` will return the result of the dropout operation applied to the input.
The result has the same tensor dimensions as the input.

## Description

The `Dropout()` operation randomly selects elements of the input with a given probability called the *dropout rate*,
and sets them to 0.
This has been shown to improve generalizability of models.

In CNTK's implementation,
the remaining values that are not set to 0 will instead be multiplied with (1 / (1 - dropout rate)).
This way, the model parameters learned with dropout are directly applicable in inference.
(If this was not done, the user would have to manually scale them before inference.)

To enable dropout in your training, you also
need to add a parameter `dropoutRate` to the `SGD` section to define the dropout rate.
This is done in the `SGD` a section, instead of a parameter to `Dropout()` itself,
in order to allow to start off a training without dropout, and then enable it after a few epochs,
which is a common scenario.
For this, the `dropoutRate` is specified as a vector, where each
value is for a specific epoch.

When running inference, the `Dropout()` operation passes its input unmodified (it is a no-op).

## Example

The following is a simple convolutional network with a dropout layer towards the end:

    features = Input{...}
    c = ConvolutionalLayer {32, (5:5), activation=ReLU} (features)
    p = MaxPoolingLayer {(3:3), stride = (2:2)} (c)
    h = DenseLayer {64, activation = ReLU} (p)
    d = Dropout (h)
    z = LinearLayer {10} (d)

In addition, you need a corresponding entry in the `SGD` section.
The following example defines to use no dropout for the first 3 epochs,
and then to continue with a dropout rate of 50%.
For convenience, this example uses the asterisk (`*`) syntax to denote repetition:

    SGD = {
        ...
        dropoutRate = 0*3:0.5
        ...
    }
