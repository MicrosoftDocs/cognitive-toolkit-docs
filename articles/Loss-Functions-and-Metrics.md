---
title:   Loss functions and metrics
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   11/08/2016
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   NA
---

# Loss functions and metrics

CNTK contains a number of common predefined *loss functions* (or *training criteria*, to optimize for in training),
and *metrics* (or *evaluation criteria*, for performance tracking).
In addition, custom loss functions/metrics
can be defined as BrainScript expressions.

## CrossEntropy(), CrossEntropyWithSoftmax()

Computes the categorical cross-entropy loss (or just the cross entropy between two probability distributions).

    CrossEntropy (y, p)
    CrossEntropyWithSoftmax (y, z)

### Parameters

* `y`: labels (one-hot), or more generally, reference distribution. Must sum up to 1.
* `p` (for `CrossEntropy()`): posterior probability distribution to score against the reference. Must sum up to 1.
* `z` (for `CrossEntropyWithSoftmax()`): input to a Softmax operation to compute the posterior probability distribution to score against the reference

### Return value

This operation computes the cross-entropy between two probability distributions
`y` and `p`, defined as:

    ce = E_y{-log p} = -sum_i y_i log p_i

with `i` iterating over all elements of `y` and `p`. For `CrossEntropyWithSoftmax()`, `p` is computed from the input parameter `z` as

    p = Softmax (z)

### Description

These functions compute the [cross-entropy](https://en.wikipedia.org/wiki/Cross_entropy) of two probability distributions. Categorical cross-entropy is the most common
training criterion (loss function) for single-class classification,
where `y` encodes a categorical label as a one-hot vector.
Another use is as a loss function for probability distribution regression, where `y` is a target distribution
that `p` shall match.

When used as a loss function, CNTK's SGD process will sum up the cross-entropy values
of all individual samples in a minibatch, and compute the per-epoch loss
by aggregating these over an entire epoch.
This is then reported in this form: `L * S` where `L` is the average loss per sample,
and `S` the number of samples. For example, `-1.213 * 3600000`, means an average cross-entropy
loss of -1.213, averaged over an epoch of 3,600,000 samples.

`CrossEntropyWithSoftmax()` is an optimization for the most common
use case of categorical cross-entropy, which takes advantage of the specific form of Softmax.
Instead of a normalized probability, it accepts as its input the argument to the Softmax operation,
which is the same as a non-normalized version of log Softmax, also known as "logit".
This is the recommended way in CNTK to compute the categorical  cross-entropy criterion.

Note that categorical cross-entropy is not a suitable loss function for multi-class labels,
where `y` contains more than one position containing a 1.
For this case, consider using [`Sigmoid()`](./BrainScript-Activation-Functions.md) instead of Softmax,
with a [`Logistic()`](#logistic-weightedlogistic) loss.
See also [this article](./How-do-I-Train-Models-in-BrainScript.md#train-a-multilabel-classifier).

#### Alternative definition
`CrossEntropyWithSoftmax()` is currently a CNTK primitive which has limitations.
A more flexible, recommended alternative is to define it manually as:

    CrossEntropyWithSoftmax (y, z) = ReduceLogSum (z) - TransposeTimes (y, z)

#### Sparse labels
To compute the cross entropy with sparse labels (e.g. read using `Input`(./Inputs#input)`{..., sparse=true}`),
the alternative form above must be used.

#### Softmax over tensors with rank>1
To compute `CrossEntropyWithSoftmax()` over When applied to tensors of rank>1, e.g. where the task is to determine a location on a 2D grid, yet another alternative form must be used:

    CrossEntropyWithSoftmax (y, z, axis=None) = ReduceLogSum (z, axis=axis) - ReduceSum (y .* z, axis=axis)

This form also allows to apply the Softmax operation along a specific axis only.
For example, if the inputs and labels have the shape `[10 x 20]`, and the Softmax should be computed
over each of the 20 columns independently, specify `axis=1`.

### Example

    labels = Input {9000}
    ...
    z = W * h + b
    ce = CrossEntropyWithSoftmax (labels, z)
    criterionNodes = (ce)

The same with sparse labels:

    labels = Input {9000, sparse=true}
    ...
    z = W * h + b
    ce = ReduceLogSum (z) - TransposeTimes (labels, z)
    criterionNodes = (ce)

## Logistic{}, WeightedLogistic{}

Computes the logistic loss function.

    Logistic (y, p)
    WeightedLogistic (y, p, instanceWeight)

### Parameters

* `y`: ground-truth label, 0 or 1
* `p`: posterior probability of being of class `1`

### Return value

Computes the (weighted) logistic loss, defined as:

    ll = -sum_i { y_i * log(p_i) + (1-y_i)*log(1-p_i))} * weight 

(where for `Logistic()`, the weight is 1).

### Description

This function is the logistic loss function.

`Logistic (y, Softmax (z))` is identical to `CrossEntropyWithSoftmax (y, z)`
for two-class problems,
where only one of the two (complementary) ground-truth labels is given to `Logistic()`.

See also [this article](./How-do-I-Train-Models-in-BrainScript.md#train-a-multilabel-classifier) on training multi-class classifiers.

### Example

    multiLabels = Input {1000}                     # 0 or 1 for each value
    ...
    p = DenseLayer {1000, activation=Sigmoid} (h)  # element-wise predictor for 1000 values
    ll = Logistic (multiLabels, p)
    trainingCriterion = (ll)

## ClassificationError{}

Computes the error rate for prediction of categorical labels. For binary labels you can use [hamming loss](./How-do-I-Train-Models-in-BrainScript.md#train-a-multilabel-classifier).

    ClassificationError (y, z)

### Parameters

* `y`: categorical labels in one-hot form
* `z`: vector of prediction scores, e.g. log probabilities

### Return value

1 if the maximum value of `z` is at a position where `y` has a 1; 0 otherwise; summed up over an entire minibatch.

### Description

This function accepts a vector of posterior probabilities, logits, or other matching scores,
where each element represents the matching score of a class or category.
The function determines whether the highest-scoring class is equal to the class
denoted by the label input `y`, by testing whether the highest-scoring position contains a 1.

When used as an evaluation criterion, the SGD process will aggregate all values over an epoch
and report the average, i.e. the error rate.

`ClassificationError()` cannot be used with sparse labels.

Note: In CNTK prior to version 1.7, this operation was called `ErrorPrediction()`.
The name can still be used, but is deprecated.

### Example

    labels = Input {9000}
    ...
    z = W * h + b
    errs = ClassificationError (labels, z)
    evaluationNodes = (errs)
