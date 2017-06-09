---
title:   BrainScript minibatchSize and Python minibatch size in samples in CNTK
author:    chrisbasoglu
date:    03/10/2017
ms.author:   cbasoglu
ms.date:   03/10/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# BrainScript minibatchSize and Python minibatch size in samples in CNTK

Note: For BrainScript users, the parameter for minibatch size is minibatchSize, for Python users, it is minibatch_size_in_samples.  The below description uses the name minibatchSize, but the description is fully applicable to Python users of minibatch_size_in_samples.

CNTK has a very specific definition of `minibatchSize` parameter: It denotes **the number of samples between model updates**.
A *sample* here is defined as one vector or tensor flowing through the system.
For example, in an image recognition task, one image is one sample.

The minibatch size for each epoch, given in *samples* (tensors along a dynamic axis). The default value is `256`. You can use different values for different epochs, e.g., `128*2:1024` (in BrainScript) or `128*2 + 1024` (in Python) means using minibatch size of 128 for the first two epochs and then 1024 for the rest.
Note that 'Minibatch size' in CNTK means the number of samples processed between model updates. This definition also holds when parallelizing across workers (e.g. for `K` workers, 
the number of samples each worker would process is `minibatchSize/K`).
In case of variable-length inputs, `minibatchSize` refers to the number of items in these sequences,
*not* the number of sequences.
SGD will try to fit up to as many sequences as possible into the minibatch that does not exceed `minibatchSize` total samples.
If several inputs are given, tensors are added to the current minibatch until one of the inputs exceeds the minibatchSize.


Importantly, for sequential data, a sample is an individual item of a sequence.
Hence, CNTK's `minibatchSize` does *not* refer to the
number of *sequences* in the minibatch,
but the *aggregate number of sequence items/tokens* across the sequences that constitute the minibatch.
CNTK has native support for variable-length sequences, i.e. it can accommodate
sequences of highly varying lengths within the same minibatch, without need for workarounds like bucketing.
Together with CNTK's notion of specifying the learning rate per sample (instead of a minibatch average),
every item of sequences of any length contributes the same to the gradient,
leading to consistent convergence.
(Many other toolkits define the minibatch size for sequential data as the number of *sequences*
in the minibatch.
This is problematic, especially if gradients are also defined as minibatch averages rather than
CNTK's minibatch sums, because the contribution to the gradient from each token or step in a sequence
would be inversely proportional to the sequence length. CNTK's approach avoids this.)

When multiple `Input{}`s are used, it is possible that not all inputs have the same sequence length.
For example, in sequence classification the label of each sequence is a single token.
In this case, the input with the largest number of samples controls the minibatch size. (You can change this behavior by specifying `definesMbSize=True` for some input, then the minibatch size will be counted based on the sequences from this particular input. When several inputs are specified, only a single one can have `definesMbSize` set to `True`.)

Despite our clear definition of `minibatchSize` being the number of samples between model updates,
there are two occasions where we must relax the definition:
* sequential data: Variable-length sequences do not generally sum up to exactly the requested
minibatch size. In this case, as many sequences as possible are packed into a minibatch without exceeding the requested
minibatch size (with one exception: If the next one sequence in the randomized corpus exceeds the
length of the minibatch size, the minibatch size will consist of this sequence).
* data parallelism: Here, the minibatch size is approximate, as our chunk-based randomization algorithm cannot guarantee
that each worker receives precisely the same number of samples.

All of the above considerations also apply to `epochSize`, but `epochSize` has some differences, see [here](./BrainScript-epochSize-and-Python-epoch_size-in-CNTK.md).

