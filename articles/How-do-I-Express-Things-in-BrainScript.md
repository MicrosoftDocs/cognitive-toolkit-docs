---
title:   How do I express things in BrainScript
author:    chrisbasoglu
date:    04/05/2017
ms.author:   cbasoglu
ms.date:   04/05/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# How do I express things in BrainScript

* [Get nice syntax highlighting for BrainScript config files](#get-nice-syntax-highlighting-for-brainscript-config-files) ? 
* [Express the error rate of my binary classifier](#express-the-error-rate-of-my-binary-classifier)? 
* [Express a softmax with a temperature parameter](#express-a-softmax-with-a-temperature-parameter)? 
* [Express a gating mechanism](#express-a-gating-mechanism)? 
* [Express a softmax over a dynamic axis](#express-a-softmax-over-a-dynamic-axis)? 
* [Zoneout](#implement-zoneout)? 
* [Build a constant 3D tensor](#build-a-constant-3d-tensor)? 
* [Combine or concatenate vectors in BrainScript](#build-a-constant-3d-tensor)? 
* [Interpret the training loss](#interpret-the-training-loss)?

## Get nice syntax highlighting for BrainScript config files

Here's how to do it if you use `vim`; other editors should be similar
```
:set syntax=conf
```
This is not perfect but it's much better than no syntax highlighting. If you know how to write a `.vim` syntax file please send us a pull request.

In Emacs, `conf-mode` is best suited. To switch, press `M-x` to start entering commands, then enter `conf-mode`. On Windows Emacs, there is also `conf-windows-mode`, however that mode appears to sometimes get confused by special characters in comments.

## Express the error rate of my binary classifier

Do not use ClassificationError as it only works for multiclass labels. Using the standard arithmetic and logical operators you can express this as
```
err = (label != (prediction > 0.5))
...
evaluationNodes = (err)  
```
where we assume here that prediction is an estimate of the probability of the positive class given the input.

## Express a softmax with a temperature parameter

A temperature softmax is very easy in BrainScript
```
TemperatureSoftmax (z, T) = Softmax (z / Constant (T))
```

## Express a gating mechanism

The simplest way is to use 

[`BS.Boolean.If`](./If-Operation.md)` (condition, thenExpression, elseExpression)`

which works with scalars as well as tensors (all arguments can [broadcast](./Binary-Operations.md#broadcasting-semantics)).

## Express a softmax over a dynamic axis

A softmax over a dynamic axis can be done via a recurrence:

BrainScript:
```
SoftMaxOverSequence (z) = {
    # define a recursive expression for \log(\sum_{i=1}^t \exp(z_i))
    runningLogSumExp = LogPlus (z,
                           PastValue (0, runningLogSumExp, defaultHiddenActivation=-1e30)) 
    logSumExp = If (BS.Loop.IsLast (runningLogSumExp),  # if last entry
                /*then*/ runningLogSumExp,              # then copy that
                /*else*/ FutureValue (0, logSumExp))    # else just propagate to the front
    result = Exp (z - logSumExp)
}.result
```

## Implement Zoneout

You might be wondering if Dropout applied to a Constant is dynamically evaluated. It is! Therefore [Zoneout](http://arxiv.org/abs/1606.01305) is as simple as 
```
Zoneout (x, xprev) =
{
    mask = Dropout (BS.Constants.OnesLike (x))
    res = BS.Boolean.If (mask, xprev, x)
}.res
```

## Build a constant 3D tensor

I want to build a constant 3D tensor in CNTK. I learned how to produce 1D and 2D constant arrays, I can stack (concatenate or combine) them and repeat them. Now I need to stack the 2D Tensors to make a 3D tensor?

Say you have three tensors, e.g.

``` 
A = ParameterTensor {100:200}
B = ParameterTensor {100:200}
C = ParameterTensor {100:200}
``` 

You can now say
 
```
    ABC = Splice (A:B:C, axis=3)
```
 
which will give you a [100 x 200 x 3] tensor.
 
(If, on the other hand, you had says Splice (A:B:C, axis=1), you would get a [300 x 200] tensor, and Splice (A:B:C, axis=2) would get you a [100 x 600] tensor.)
 
Note that to splice in a new dimension, dimensions of all inputs must match. E.g. you cannot stack a ParameterTensor {100:200} and a ParameterTensor {100:50} along axis 3 (but you could stack it along axis 2, which wold give you a [100 x 250] tensor).
 
Also note that axis indices are 1-based, like in math. E.g. the row dimension of a matrix is the first axis, axis=1, and the column dimension is the second axis, axis=2.

## Interpret the training loss

How does CNTK calculate the training loss (as bolded) after an epoch is finished? Does the final model go through the whole training set to get the value, or does it simply average the miniBatch training losses produced by the model while it gets  updated during the epoch?   
<pre><code>Finished Epoch[ 1 of 100]: [Training] <b>CE = 0.15920662 * 59999999</b>; totalSamplesSeen = 59999999; learningRatePerSample = 9.9999997e-05; epochTime=641.392s
Final Results: Minibatch[1-3510]: CE = 0.32190538 * 3592929
Finished Epoch[ 1 of 100]: [Validate] CE = 0.32190538 * 3592929</pre></code>

The answer is the latter (where the averaging is weighted by the samples in the minibatch in case they are variable-length). I.e. you do not get the actual training loss of the model in the state it is in at the end of the epoch.