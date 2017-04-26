---
title:   Gather and Scatter
author:    chrisbasoglu
date:    09/22/2016
ms.author:   cbasoglu
ms.date:   09/22/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# Gather and Scatter

## Gather(), Scatter()

Shorten or lengthen a data sequence based on a mask.

    BS.Sequences.Gather  (maskSequence, dataSequence)
    BS.Sequences.Scatter (maskSequence, dataSequence)

### Parameters

* `maskSequence`: a sequence of 0 or 1 (static tensor dimension `[1]`), where 1 indicate samples to keep. (Any other value gives rise to undefined behavior and may change in the future.)
* `dataSequence`: the data sequence to process. For `Gather()`, its length must be the same as that of `maskSequence`, while for `Scatter()`, it must be equal to the number of ones in `maskSequence`.

### Return value

For `Gather()`, a sequence that contains a subset of `dataSequence`, with some samples dropped. The length of the result sequence will be equal to the number of ones in `maskSequence`.

For `Scatter()`, a sequence that contains samples of `dataSequence` and inserted zeroes. The result sequence will have the same length as `maskSequence`.

### Description

`Gather()` and `Scatter()` allow you to shorten or lengthen an input sequence, such as a sequence of words, based on a mask sequence. 

`Gather()` returns the sub-sequence of those samples `dataSequence[n]` for which `maskSequence[n]` is `1`, while those for which the mask is 0 will be dropped.

`Scatter()` takes elements of a sub-sequence and places them in a longer sequence, inserting zeroes.

For example:

    g = Gather  (m, d)
    s = Scatter (m, g)

    d = A B C D E F G H I J K L M N O P Q R S T U V W X Y Z  # data
    m = 1 1 0 1 0 0 1 0 1 1 0 0 1 0 0 1 1 0 0 1 0 0 1 1 0 1  # mask
    g = A B D G I J M P Q T W X Z
    s = A B 0 D 0 0 G 0 I J 0 0 M 0 0 P Q 0 0 T 0 0 W X 0 Z

`Gather()` is often used indirectly through `Slice(..., axis=-1)`, `BS.Sequences.First()`, and `BS.Sequences.Last()`.

### Examples

#### Recurrent language model

Given a word sequence of the form `<s> A B C ... </s>`, a language model takes the `input` of the form `<s> A B C ...` to predict `labels` of the form `A B C ... </s>`. `Gather()` allows a single word sequence to be read
from file, and then create the `input` and `labels` by dropping the trailing `</s>` and leading `<s>`, respectively:

    words  = Input {inputDim}
    inputMask  = !BS.Sequences.IsFirst (words)
    labelsMask = !BS.Sequences.IsLast  (words)
    input  =                       Gather (inputMask,  words)          # drop trailing </s>
    labels = ReconcileDynamicAxis (Gather (labelsMask, words), input)  # drop leading <s>

    lstmLM = Sequential
    (
        EmbeddingLayer {300} :
        RecurrentLSTMLayer {512} :
        DenseLayer {inputDim, activation=LogSoftmax}
    )

    logP = lstmLM (input)
    ce = -TransposeTimes (labels, logP)
    criterionNodes = (ce)

Note: The `Gather()` operation can more conveniently be written as follows, which under the hood calls `Gather()`:

    input  =                       Slice (0, -1, words, axis=-1)          # strip trailing </s>
    labels = ReconcileDynamicAxis (Slice (1,  0, words, axis=-1), input)  # strip leading <s>

#### Thought vector

The following selects the last sample of a hidden-state sequence:

    h = RecurrentLSTMLayer {} (x)       # sequence of hidden states
    lastMask = BS.Sequences.IsLast (h)  # mask to denote the last step
    thoughtVector = BS.Sequences.Gather (lastMask, h)  # select the last

# IsFirst(), IsLast()

Create a mask for use with `Gather()` to select the first and last sample of a sequence, respectively.

    BS.Sequences.IsFirst (dataSequence)
    BS.Sequences.IsLast  (dataSequence)

## Parameters

* `dataSequence`: any sequence, e.g. a word sequence

## Return value

A mask for use with `Gather()` that will select the first or last item, respectively, while dropping all other samples.

## Description

`IsFirst()` and `IsLast()` create a mask for use with `Gather()` to select the first and last sample of a sequence, respectively, which is typically needed in sequence-to-sequence models to extract the "thought vector" (the
last hidden state of a recurrence).

## Examples

See [here](#thought-vector).
