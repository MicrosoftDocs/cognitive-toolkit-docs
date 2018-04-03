---
title:   Sequential 
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   08/26/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   brainscript
---

# Sequential

Composes an array of functions into a new function that calls these functions one after another ("forward function composition").

    Sequential (arrayOfFunctions)

## Parameters

`arrayOfFunctions`: a BrainScript array of functions, e.g. constructed with the `:` operator: `(LinearLayer{1024} : Sigmoid)`

## Return value

This function returns another function. That returned function takes one argument, and returns the result
of applying all given functions in sequence to the input.

## Description

`Sequential()` is a powerful operation that allows to compactly express a very common situation in neural networks
where an input is processed by propagating it through a progression of layers.
You may be familiar with it from other neural-network toolkits.

`Sequential()` takes an array of functions as its argument,
and returns a *new* function that invokes these function in order,
each time passing the output of one to the next.
Consider this example:

    FGH = Sequential (F:G:H)
    y = FGH (x)

Here, the colon (`:`) is BrainScript's syntax of expressing arrays. For example,
`(F:G:H)` is an array with three elements, `F`, `G`, and `H`.
In Python, for example, this would be written as `[ F, G, H ]`.

The `FGH` function defined above means the same as

    y = H(G(F(x))) 

This is known as ["function composition"](https://en.wikipedia.org/wiki/Function_composition),
and is especially convenient for expressing neural networks, which often have this form:

         +-------+   +-------+   +-------+
    x -->|   F   |-->|   G   |-->|   H   |--> y
         +-------+   +-------+   +-------+

which is perfectly expressed by `Sequential (F:G:H)`.

Lastly, please be aware that the following expression:

    layer1 = DenseLayer{1024}
    layer2 = DenseLayer{1024}
    z = Sequential (layer1 : layer2) (x)

means something different from:

    layer = DenseLayer{1024}
    z = Sequential (layer : layer) (x)

In the latter form, the same function *with the same shared set of parameters* is applied twice,
while in the former, the two layers have separate sets of parameters.

## Example

Standard 4-hidden layer feed-forward network as used in the earlier deep-neural network
work on speech recognition:

    myModel = Sequential (
        DenseLayer{2048, activation=Sigmoid} :  # four hidden layers
        DenseLayer{2048, activation=Sigmoid} : 
        DenseLayer{2048, activation=Sigmoid} : 
        DenseLayer{2048, activation=Sigmoid} : 
        DenseLayer{9000, activation=Softmax}    # note: last layer is a Softmax 
    )
    features = Input{40}
    p = myModel (features)
